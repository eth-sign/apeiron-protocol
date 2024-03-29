---
eip: 
title: Apeiron
author: German Abal Bazzano (@ariutokintumi)
discussions-to: https://twitter.com/ApeironProtocol
type: Standards Track
category: ERC
status: Pre-Draft
created: 2022-10-16
updated: 2023-05-23
requires: 
---

## Simple Summary

A fully decentralized and secure tokenization protocol that ensures complete ownership of tokens, referred to as 'Signs'. These Signs maintain a direct on-chain correlation with what they represent and can move through the grid formed by all the protocol users. Additionally, each Sign can be authenticated through digital signatures embedded within its metadata.

## Abstract

The following standard allows for the implementation of a standard API for Signs within Consoles (Proxy Smart Contracts) and Cartridges (Execution Smart Contracts). This standard provides functionality to deploy a Console and a Cartridge (the first Cartridge version IS named 'Pong' and we are trying to push the first standard in the use cases) which guarantees their full compliance to the Apeiron Protocol using EXTCODEHASH whitelists. 

The Console is responsible for creating, storing, and deleting Signs by modifying ONLY their metadata and shielding the integrity of the tokens mapping. Cartridges runs the functions to operate with the Signs and transacts with remote Consoles only by compliance. The Console will mint and burn Signs and modify ONLY the metadata of the Signs, by the Cartridge requests, not being capable to modify other token information at all. The Signs validation/certification happens on the metadata layer and is, in most cases, an off-chain process, unless a user wants to store it fully on-chain, something not relevant at this point.

Diagram of the Apeiron Protocol Handshake:

                             +-----------+                                  +-----------+ 
                             |   Signs   |                                  |   Signs   |
                             +-----------+                                  +-----------+ 
        User ----- tx -----> | Console 1 | <------ EXTCODEHASH CHECK -----> | Console 2 |
                             +-----------+                                  +-----------+
                             | Cartridge |                                  | Cartridge |
                             +-----------+                                  +-----------+ 

On the Handshake both Consoles check the EXTCODEHASH of the remote Console and remote Cartridge. If both (Console and Cartridge) are whitelisted, for both of the Consoles, it proceeds with the requested User transaction, which can go through or be reverted depending on other circumstantial factors but not depending on the protocol Handshake. i.e. an unexpected token pull/push request.

Note that for the Apeiron Protocol compliance, both Consoles smart contracts MUST have whitelisted the remoete EXTCODEHASH but MUST NOT be the same Console contract address, while the Cartridge smart contracts address could be the same (not strictly necessary but in fact the Catriges reusing is the recommendation) since the code MUST be the expected.

We have considered use cases where Signs are minted, owned, and transacted by Consoles through the Cartridges compliance functionality. Consoles are owned only by individuals and transacted by the owner and other Consoles, with the option to grant permissions to third-party 'operators'. Signs are a representation token that provides total ownership (not just rights) of any hashable or relational thing, object, and asset, using a direct relational algorithm/fingerprint of it, something that MUST be defined by each project information. The Signs have the property to represent anything on-chain, so they can be directly related to digital content, abstraction, or physical object. We have considered a diverse universe of assets, and we know you will dream up many more. The goal is to achieve decentralization, ownership, relationship, and verification, something NFTs (ERC-721 and ERC-1155) cannot deliver, but many other opportunities are coming with this Console/Cartridge architecture.

To accomplish this objective, we need to ensure the origin and credibility of the tokens. Thus, the Consoles provide the users and Cartridges the ability to mint Signs but never alter them, except for the metadata which can change as many times as is needed. This guarantees that the tokens cannot be duplicated or re-minted as the same, not even by the Console owner in the same Proxy Smart Contract Storage.

Let's explore the structure of the Apeiron Protocol token (Signs) mapping on the Console Storage. This mapping has only the write permission to the Console functions and cannot be directly altered by Cartridges or the smart contract owner.

        +-----------------+--------------+----------------+
        | string tokenId  | uint256 key  | byte metadata  |
        +-----------------+--------------+----------------+

- The 'tokenId' is created alongside the token and remains unchangeable, thereby preserving the token's intended relationship. It can only be removed when the token is burned by the Console function.
- The 'key' is an auto-incrementing value that never decreases, regardless of whether a lower value is deleted. This design prevents token impersonation. It can only be removed when the token is burned.
- The 'metadata' comprises binary code of compressed metadata using the 'brotli' algorithm. Since this computation is always performed off-chain, this will save data and storage tx costs. It can be empty if the user hosts it's own tokens metadata and configured the Cartridge to avoid writting it down in Storage. This can be done by the combination of optional configurations.

Here is a list of what Signs can tokenize:

- images, videos, digital content, contracts, relational data, POAPs, music, books, properties, canvas, strokes, logos, graffiti, sounds, fingerprints, diamonds, documents, sign language, smells, patents, plans, molecules, text, radio frequencies, secret messages and anything else that can be hashed, even time!

Through the use of metadata, the Signs can embed or be associated with the content that generated it or any other content. If it is the latter case, the token can be considered as a key that requires knowledge of what generated it to identify and access the associated content, a secret message. This is later explained in the Metadata section. The content possibilities include, but are not limited to, all the current use cases of NFTs.

NFTs lack a direct on-chain relationship with the "thing" they represent, as per the standard. Additionally, the ownership system is weak, often amounting to a third-party centralized right to own. Also, in many cases, the smart contract owner retains full control over the NFTs, allowing him to change the ownership of the tokens.

Furthermore, there is no standard protocol for NFTs to prevent common smart contract vulnerabilities, such as undesired metadata replacement or loss of ownership rights, also there is a weak relationship between tokens and their intended representations. In contrast, Signs have an explicit relationship with their 'tokenId', representing only one thing and preventing the possibility of obtaining a different output from what generated it. As a result, the proof of the relationship is stored on-chain, ensuring greater security and reliability.

The ownership associated with NFTs is, at best, a right that produces ownership through the interpretation of smart contract rules and a function called "owner." However, this ownership is in fact just a right that can be revoked in some cases. If we want to achieve true ownership and decentralization, we need to own our tokens that represent something verifiable and reside in our own smart contract. From there, we can build any dApp that requires this structure. This is exactly what happens with Signs, Consoles, and Cartridges, where tokens are literally created, owned, and transferred between a grid of all the compliance Consoles and interact with the desired Cartridges, which act as nodes in the grid and enable the possibility to move in all directions and also extend the functionality.

I know that probably many developers are wondering now "How can I mint a 10,000 tokens collection with this shielded standard that allows 1 by 1 mint and needs to use a lot of txs (and so time and gas) for it?", a task that is straightforward with NFTs. I understand that this will be a main issue in the mass adoption problems, so I would love if the community helps to build a very simple standard Cartridge to create an easy 'N tokens Collection mint' without many options on the contract, as happens with 69 of every 420 NFT space collections. Well, you will find a standard interface suggestion for this in the 'Community Consensus' section.

ould love if the community decides to name this 'N tokens Collection mint' as 'Asteroids'. Given the differing EXTCODEHASH this can be implemented with the standard from scratch as well, to avoid each user need to whitelist the 'Asteroids' Cartridge, something that naturally needs also the mandatory community audits and opens the space for scams very fast. By doing this the users can enjoy the new possibilities that this Cartridge would bring without taking a risk, making the flow from NFTs to Consoles soft.

Many security and critical considerations are taken and listed in the sections, such as the capability of block incomming tokens, time-lock owner-changes and storage collisions.

## Motivation

A standard interface facilitates wallets, brokers, social, gaming, and marketplace dApps to interact seamlessly and with confidence with any Console on the Ethereum network grid. This is particularly provided for Apeiron Protocol smart contracts, otherwise known as Consoles, vaults, or nodes of the grid, that are capable of owning and managing Signs through compliance Cartridges. Further applications will be explored in the following sections.

This Apeiron Protocol token standard builds upon the well-known ERC-721 token standard, drawing from four years of collective experience since its introduction. However, ERC-721 design has inherent centralization, security shortcomings, and an insufficient framework for correlating tokens with their intended representations. This is primarily due to the lack of direct on-chain connection or relationship between each asset and its representation, thus requiring off-chain information for this purpose. In stark contrast, Signs are fully decentralized, and secure, and provide complete ownership to the Console owner. They are also directly connected to their representation on-chain, thereby offering a more robust and reliable solution for asset ownership and verification.

## Specification

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

**Every Apeiron Protocol compliant contract must be verified by the `ERC1942compliance` function before any transaction** (subject to "caveats" below):

```solidity
pragma solidity ^0.8.0;

/// @title Apeiron Protocol
/// @dev See https://github.com/eth-sign/apeiron-protocol
/// Note: the Apeiron Protocol Console and Cartridge interface is under development, be patient 
/// while we finish this approach to deliver something great to the community. There is many bugs 
/// or pseudo-code in here right now, I like to work public so visitors can see the pgrogress and 
/// visualize the 'spirit' of the EIP.

interface APEIRON {
    /// @dev This emits when an Sign will be sent to a Console. This event
    /// emits unit_256 _key value when Signs are created (`from` == 0).
    /// Exception: Any number of Signs can be created and assigned without
    /// emitting Transfer. At the time of any transfer, the _key Sign is deleted 
    /// but _key (global autoincremental value) doesn't occurs.
    event Transfer(address indexed _to, string indexed _tokenId, uint256 indexed _key);
    
    /// @dev This emits when pre-aproval to a Console address to pull a Sign is 
    /// changed or reaffirmed. The zero address indicates there is no approved Console 
    /// address. When a Transfer event emits, this also indicates that the approved
    /// Console for that Sign is reset to none.
    event pullApproval(address indexed _approved, string indexed _tokenId, uint256 indexed _key);
    
    /// @dev This emits when an operator is enabled or disabled for managing the Console.
    /// The operator can manage all the Signs in this Console.
    event ApprovalForAll(address indexed _operator, bool _approved);

    /// @dev This emits when a Console is locked or unlocked for receiving tokens.
    /// If is locked (true) no receiveTransfer action can be executed by others than the 
    /// whitelisted Consoles.
    event SetLocking(bool _approved);
    
    /// @dev This emits when an whitelisted is enabled or disabled for managing the Console.
    /// The whitelisted contract can push Signs in this Console bypassing a possible Locked status.
    event WhiteListed(address indexed _whitelisted, bool _approved);    

    /// @notice Query if an address is an authorized operator for the Console
    /// @param _whitelisted The address that will be able to bypass a Locked status on the Console.
    /// @return True if `_whitelisted` is an approved Console, false otherwise
    function isWhisteListed(address _whitelisted) external view returns (bool);
    
    /// @notice Query if an contract address is an authorized operator for the Console
    /// @param _operator The address that acts on behalf of the Console owner
    /// @return True if `_operator` is an approved operator, false otherwise
    function isApprovedForAll(address _operator) external view returns (bool);

    /// @notice Count all Signs on existance in the Console
    /// @return The number of Signs on existance, possibly zero
    function balanceOf() external view returns (uint256);
    
    /// @notice Check the existence of an Sign and if yes, the number of clones
    /// @dev 0 means there is no Signs in the Console, 1 means a true or just 1 Sign,
    /// a different number means a quantity including the 'clones', a "clone' is a
    /// Sign with same 'tokenId', but not neccessary same 'metadata'.
    /// @param _tokenId is the Sign to check
    /// @param _key is the optional _key of the Sign to check
    function checkOf(string _tokenId, uint256 _key) external view returns (uint256);

    /// @notice Find the pointer of a Console.
    /// @dev Signs pointing to nothing are NOT considered invalid, since the 'tokenId'
    /// could be sufficient to relate it to the asset it represents.
    /// @return The _pointer of the vault directory in IPFS
    function pointer() external view returns (_pointer);

    /// @notice Changes the data pointer of the Console from one to another, this changes 
    /// the association.
    /// @param _pointer The string data Pointer to URI to change to
    function changeDataPointer(string _pointer) external payable;

    /// @notice Mint a Sign 
    /// @dev Throws unless `msg.sender` is the current owner or an authorized
    /// operator for this Console. This outputs the _key value (`from` == 0)
    /// for this Sign.
    /// @param _tokenId The Sign token ID product of the dev choosen algorithm or hash
    function Mint(uint256 _tokenId) external payable (_key);

    /// @notice Transfers the ownership of an Sign from our Console to another Console
    /// @dev Throws unless `msg.sender` is the current owner, an authorized
    ///  operator for this Console or if `_to` is the zero address. 
    ///  Throws if `_key` is not valid. When transfer is complete, this function calls 
    /// `onERCApeironReceived` on `_to` and throws if the return value is not
    /// `bytes4(keccak256("onERCApeironReceived(address,uint256,uint256,string)"))`.
    /// @param _to The new Console
    /// @param _key The Sign token key value to transfer
    function safeTransferFrom(address _to, uint256 _key) external payable;

    /// @notice This function let pre-aprove a Sign pullRequest transfer
    /// to some specific Console
    /// @dev Throws unless `msg.sender` is the current owner or an authorized
    ///  operator for this Console.
    /// @param _to The Console approved to pull the Sign
    /// @param _key The Sign token key to be approved for transfer
    function pullFunction (address _to, uint256 _key) external payable;

    /// @notice Process a request to pull a pre-approved Sign to the requestion Console
    /// @dev Throws unless `msg.sender` is the pre-approved Console for the Sign token
    /// @param _to The Console approved to pull the Sign
    /// @param _key The Sign token key to be approved for transfer
    function pullRequest (address _to, uint256 _key) external payable;

    /// @notice Receives a token from a Console
    /// @dev Throws unless `msg.sender` is an Console Apeiron Protocol compliance checked by
    /// ERC1942compliance function or Locking is 'true'. If address _from is 
    /// whitelisted the function will continue only if `msg.sender` is an Console 
    /// Apeiron Protocol compliance.
    /// @param _to The Console approved to pull the Signs
    /// @param _key The Sign token key to be approved for transfer 
    function receiveTransfer (address _from, uint256 _key, pointer) external payable;
	
    /// @notice This configures an operator to managing the Console.
    /// @dev Throws unless `msg.sender` is the current owner or an authorized
    ///  operator for this Console. 
    ///  The operator can manage all the Signs in this Console.
    /// @param _operator The _address approved to become operator
    /// @param _approved The value `true` or `false` to configure the state
    function ApprovalForAll(address _operator, bool _approved) external payable;
    
    /// @notice This adds a Console address into the Whitelist to bypass a locked state.
    /// @dev Throws unless `msg.sender` is the current owner or an authorized
    ///  operator for this Console. 
    /// The whitelisted Console can use the receiveTransfer if the Locked state is 'true'.
    /// @param _whitelisted The _address approved to become a whitelisted Console.
    /// @param _approved The value `true` or `false` to configure the state
    function WhiteListed(address _whitelisted, bool _approved) external payable;

    /// @notice This changes the Console state to be locked or unlocked what prevents to
    /// call the receiveTransfer function.
    /// @dev Throws unless `msg.sender` is the current owner or an authorized
    /// operator for this Console. 
    /// Any Console can use the receiveTransfer to send Signs (by the protocol) into this vault 
    /// when Locking is false.
    /// @param _approved The value `true` or `false` to configure the state
    function SetLocking(bool _approved) external payable;

    /// @notice Query if a remote Console is compliance with Apeiron Protocol using the EXTCODEHASH
    /// @stored The function getRemoteExtCodeHash uses less than 700 gas
    /// @return a string `true` if the remote contract EXTCODEHASH is the expected or 
    /// `false` if is not.
    function ERC1942compliance(address _to) external view returns (bool);
    
    
    /// Below this comment is a list of final draft functions that 99.9% will exists, and for sure 
    /// replace the existing ones
    
//////////////////////////////////////////////////////
//////////////////////////////////////////////////////
//////////////////////////////////////////////////////
// APEIRON PROTOCOL CONSOLE
// 
// Console Functions:
// 
// 	Token Management:
// 		-'create_token' creates a new token (owner, operator, cartridge)
// 		-'delete_token' deletes an existing token (owner, operator, cartridge)
// 
// 	Metadata:
// 		-'token_metadata' writes the 'metadata' of a specific token (owner, operator, cartridge)
// 		-'metadata_base_uri' writes the general Console base URI for metadata 'metadata_base_uri_value' (owner, operator, cartridge)
// 		-'metadata_resolver' let user choose if metadata should be resolved with 'metadata_base_uri_value' OR each token 'metadata' on mapping, useful when the user self-host all his metadata (owner, operator, cartridge) 
// 		-'write_received_metadata', let te user opt to write the received 'metadata' for each incoming token transfer (when is sent) OR ignore it. The data can always be recovered from on-chain tx data (owner, operator, cartridge)
// 		-'show_token_metadata' resolves and outputs a specific token metadata (owner, operator, cartridge)
// 
// 	Compliance:
// 		-'consoles_whitelist' add or remove a EXTCODEHASH from the Consoles whitelist 'console_whitelists_values' (owner, operator)
// 		-'cartridge_whitelist' add or remove a EXTCODEHASH from the Cartridges whitelist 'cartridge_whitelists_values' (owner, operator)
// 		-'cartridge_socket' plug, change or unplug a Cartridge, it checks 'cartridge_whitelist_values' and 'cartridge_delay_value' (owner, operator)
// 		-'tx_function' executes the Handshakes (Console & Cartridge) and then the transaction forward. Checks 'consoles_whitelist_values', 'cartridge_whitelist_values', 'cartridge_delay_value' and 'console_delay_value' (cartridge)
// 
// 	Locking:
// 		-'gstate' locks all the 'Complianlce', 'Owner Change', 'Operator' and 'Locking' parameters in the Console, except this one, that depends on 'glockdelay_value' (owner)
// 		-'glockdelay' changes the delay applied to a change the state of 'gstate_value' from 'locked' to 'unlocked'; from 'unlocked' or 'unlocking' to 'locked' is instant (owner)
// 		-'console_delay' changes the delay applied to a recently added Console's EXTCODEHASH before it becomes compliant (owner)
// 		-'cartridge_delay' changes the delay applied to a recently added Cartridge's EXTCODEHASH before it becomes compliant and also can be plugged in (owner)
// 
// 	Owner Change:
// 		-'pre_approve_new_owner' adds or deletes an address or ANY to the 'pre_aprobe_new_owner_values' list, adding one entry depends on 'gstate', but deleting is an instant action (owner)
// 		-'new_owner_pull_request', limited to the 'pre_aprobe_new_owner_values' addresses, if there is a match of the calling address within the list, the 'owner' address is changed to the calling address.
// 
// 	Operator:
// 		-'operator' adds or deletes an address or ANY to the 'operator_values' list, adding one entry depends on 'gstate', but deleting is an instant action (owner) 
// 
// Console Storage:
// 
// 	Token integrity:
// 		-mapping 'tokenId' (restricted write), 'key' (restricted write), metadata (public write)
// 
// 	Compliance related:
// 		-'console_whitelists_values' EXTCODEHASH, timestamp (Console)
// 		-'cartridge_whitelists_values' EXTCODEHASH, timestamp (Console)
// 		-'connected_cartridge_address' address (Console)
// 
// 	Metadata related:
// 		-'metadata_base_uri_value' string
// 		-'metadata_resolver_value' to switch from each token 'metadata' value or construction using the 'metadata_base_uri_value'.
// 		-'write_received_metadata_value' binary value (1 or 0 / yes or no).
// 
// 	Locking:
// 		-'gstate_value' can be '0' (unlocked), '1' (locked), '2' (unlocking), timestamp.
// 		-'glockdelay_value' amount of time that must elapse before a change from 'locked' to 'unlocked' in the 'gstate_value' is accepted.
// 		-'console_delay_value' amount of time that must elapse for a recently added Console's EXTCODEHASH to become compliant.
// 		-'cartridge_delay_value' amount of time that must elapse for a recently added Cartridge's EXTCODEHASH to become compliant and be accepted to be connected.
// 
// 	Permissions:
//		-'owner' address of the Proxy Contract 'Console' owner.
// 		-'pre_aprobe_new_owner_values' list of addresses that can request an 'owner' change in their behalf.
// 		-'operators_values' operator addresses that have the ability to manage Console functions related to common usage (a more detailed description will be provided in future commits).
// 
// Note: The Console can permit Cartridges to use its Storage (consideration might be given to creating Storage Gaps to facilitate this). The Console's storage can be utilized for the Cartridges' own purposes. For instance, an Apeiron Protocol compliant 'Pong' Cartridge can store its data in the Console. A point of discussion could be whether storage mappings should be restricted to certain Cartridges or if they should be accessible to any Cartridge.
// 
// Console Storage for Apeiron Protocol Cartridge functions:
// 
// 	Token Management:
// 		-'push_waiting_list' waiting list to be used by 'push_i_token' function and written by 'token_waiting' function, it contains 'remote tokenId', 'remote key' + 'remote Console address'.
// 		-'pull_token_pre_approved_list' whitelist to be used by 'pull_o_token' function and written by 'token_unlock', it contains 'remote addresses OR any' that can request one or more 'token key'.
// 
// 	Locking:
// 		-'istate_value' can be '0' (unlocked), '1' (locked), '2' (unlocking), timestamp.
// 		-'idelay_value' amount of time that must elapse before a change from 'locked' to 'unlocked' in the 'istate_value' is accepted.
// 		-'iwaitlist_values' list of Console addresses that can always send us tokens without depending on the 'istate' (unless they are in the 'iblacklist', which prevails)
// 		-'iblacklist_values' list of Console addresses that will never be able to send us tokens, no matter what other rules allow, this one prevails.
// 		-'ostate_value' can be '0' (unlocked), '1' (locked), '2' (unlocking), timestamp.
// 		-'odelay_value' amount of time that must elapse before a change from 'locked' to 'unlocked' in the 'ostate_value' is accepted.
// 		-'owaitlist_values' list of Console addresses to which we can always send tokens without depending on the 'ostate' (unless they are in the 'oblacklist', which prevails)
// 		-'oblacklist_values' list of Console addresses to which we can never send tokens, regardless of what other rules allow it, this one prevails.
// 
//////////////////////////////////////////////////////
//////////////////////////////////////////////////////
////////////////////////////////////////////////////// 
// APEIRON PROTOCOL CARTRIDGE 'PONG'
// 
// Cartridge Functions:
// 
// 	Token Management:
// 		-'push_i_token' receives token via push, requires the 'istate_value' unlocked OR the remote Console in the 'iwhitelist_values' OR the transaction in the 'push_waiting_list'.
// 		-'push_o_token' sends a token via push to a remote Console.
// 		-'pull_o_token' sends a token via remote 'pull' request, requires the local token to be in the 'pull_token_pre_approved_list' and the remote console address approved to do the 'pull'.
// 		-'pull-i-token' requests a 'pull' for a specific token to a remote Console.
// 		-'token_unlock' writes the 'pull_token_pre_approved_list' with the tokens that can be pulled from a remote Console address (or ANY).
// 		-'token_waiting' writes the 'push_waiting_list' of tokens that we are awaiting to receive that match the follow mapping entry: 'remote tokenId', 'remote key' + 'remote Console address'.
// 
// 	Cartridge Lock Management:
// 		-'istate' if is enabled (locked) blocks all the the incoming token transfers, except 'iwhitelist' addresses.
// 		-'idelay' changes the delay applied to a change the state of 'istate' from 'locked' to 'unlocked'; from 'unlocked' or 'unlocking' to 'locked' is instant
// 		-'iwaitlist' Console addresses for which token incomming transfers are accepted regardless of the 'istate' value.
// 		-'iblacklist' Consoles addresses to deny all token incomming transfers regardless of 'istate' or 'iwhitelist', 'iblacklist' overrides any of these settings.
// 		-'ostate' if is enabled (locked) blocks all the the outgoing token transfers, except 'owhitelist' addresses.	
// 		-'odelay' changes the delay applied to a change the state of 'ostate' from 'locked' to 'unlocked'; from 'unlocked' or 'unlocking' to 'locked' is instant
// 		-'owaitlist' Console addresses for which we can always make outgoing token transfers, regardless of the value of 'ostate'.
// 		-'oblacklist' Consoles addresses to deny all token outgoing transfers regardless of 'ostate' or 'owhitelist', 'oblacklist' overrides any of these settings.
// 
////////////////////////////////////////////////////// 
////////////////////////////////////////////////////// 
////////////////////////////////////////////////////// 
// Notes:
//	-All functions labeling is prvisional and for clear reference.
//	-All security and locking functions are specifically intended for experienced and advanced users. Regular users are advised to utilize the standard configurations offered by the dApp with which they interact with their contracts.
//	-The inclusion of delays is meant to give to users the chance to undo any unwanted changes. It is advised to set up off-chain alerts to effectively monitor the status of preferred Consoles.
//	-Delays can be capped at a maximum time limit to prevent permanent loss of control.
//	-An easy-to-configure multi-signature 'owner' change can be considered to prevent a 'loss' or an 'infinite-locking-loop' scenario, which could occur if the Console owner's private keys are compromised.

    /// Here finish the list of the drafted functions.
    
}
```
A wallet/broker/auction application MUST implement the **wallet interface** if it will accept safe transfers.

The **metadata extension** is OPTIONAL for Apeiron Protocol smart contracts (see "caveats", below). This allows your smart contract to be interrogated for its name and for details about the assets which your Signs is associated to.

```solidity
/// @title Apeiron Protocol Sign, optional metadata extension
/// @dev See https://github.com/eth-sign/apeiron-protocol
interface ERCApeironMetadata /* is Apeiron Protocol */ {
    /// @notice A descriptive name for a collection of Signs in this contract
    function name() external view returns (string _name);

    /// @notice An abbreviated name for Signs in this contract
    function symbol() external view returns (string _symbol);

    /// @notice A distinct Uniform Resource Identifier (URI) for a given asset.
    /// @dev Throws if `_key` is not a valid Sign. URIs are defined in RFC
    ///  3986. The URI may point to a JSON file that conforms to the "Apeiron Protocol
    ///  Metadata JSON Schema". It can be compressed using 'brotli' algorithm.
    function tokenURI(uint256 _key) external view returns (string);
}
```

This is the "Apeiron Protocol Metadata JSON Schema" referenced above.

```json
{
    "title": "Asset Metadata",
    "type": "object",
    "properties": {
        "validator_n": {
            "type": "string",
            "description": "Here is recommended to sign a combination of the 'tokenId', 'key', 'contract_address', 'contract_owner', 'chain' and the follow metadata values, with any other information that the token issuer or validatior (the user or third party) considers appropiate by the use that the token will be given, such a 'past_owner' to avoid graph the blockchain if the dApp using the protocol is redundant enough (anyway there will always be the registry). By 'n' validator that is proposed for implementation of a validation as a trusted source of information for authenticity or certification. This can be implemented with consensus protocols, open protocols, private rings, centralized protocols, etc."
        }       
        "protected_code": {
            "type": "string",
            "description": "Optional, BASE64 string of the encrypted ''name', 'description' and 'value' and other metadata values. Optional and just used when the metadata is encrypted using a key directly related of what generated the token with an standard hash algoritm to be determined, different from the used to create the 'tokenId'. This MUST NOT be used in conjuntion with 'content_generator', this is a endless use cases security function."
        }        
        "content_generator": {
            "type": "string",
            "description": "Optional, this refers to the input thing that generated the tokenId through the direct relational algotithm/fingerprint. This MUST NOT be used in conjunction to 'protected_code' and is used when the token represents, in example, a 'house contract' and the token shows a picture of the house, so the tokenId is generated and represents the 'house contract' where is a direct relation, but the representation is the house picture, naturally a good option for human interpretation"
        }        
        "name": {
            "type": "string",
            "description": "Identifies the asset to which this FCS represents"
        },
        "description": {
            "type": "string",
            "description": "Describes the asset to which this FCS represents"
        },
        "representation_n": {
            "type": "string",
            "description": "A URI pointing to a resource the asset to which this Sign represents. Usually an image, video or sound, but it can be any file. There can be 'n' representation values and is up to the dApp to choose which/s to show to the user."
        }
    }
}
```

### Caveats

This pre-draft is derived from the ERC-721 proposal as 'template'. As such, this section of this document might appear to be out of context or incomplete. We acknowledge these potential inconsistencies and are actively learning and working towards refining this draft.

## Rationale

There are many proposed uses of Ethereum smart contracts that rely on tracking distinguishable assets, but existing asset standards such as NFTs fail to deliver on users expectations for: Decentralization, Ownership, Relationship, and Verification.

**"Consoles" and "Cartridges" Word Choice**

The Proxy smart contract is responsible for creating, securing, and storing tokens, based on requests from the Execution smart contract, which operates under a specific, changeable configuration. The Proxy consistently ensures protocol compliance from all counterparties, working solely under the agreed-upon standard. This is achieved by maintaining checks with remote Proxies and Execution smart contracts in every transaction. It is anticipated that the community of developers will create a variety of Execution smart contracts, offering diverse use cases and promoting widespread adoption of their protocol implementation. That's why we've dubbed the Proxy smart contracts 'Consoles' and the Execution smart contracts 'Cartridges'. Future versions of both can emerge, always striving to maximize potential while safeguarding the redundancy and security of the token Storage.

**"Signs" Word Choice**

'Signs' is a reference to 'signature', inspired by one of the primary features of Apeiron Protocol tokens: their relationship with the entity they originate from. This term found wide acceptance among virtually everyone who was NOT surveyed, and is applicable to an extensive range of distinguishable digital assets. Previously, we used a different name, FCS (First Come Signature), due to its relevance in the context of social networks, something that originated and inspired us to build this standard. However, we ultimately decided that 'sign' is more descriptive for most applications of this standard. Therefore, we will refer to these as 'sign' or 'signs' moving forward. (and back!).

*Alternatives considered: FCS, syn, Apeiron (now is the Protocol name), real NFT, NFT*

**Sign Identifiers**

Each Sign is identified by a unique pair of 'string' and 'uint256 auto-incremental' values, namely the 'tokenId' and the 'key' respectively, within an Apeiron Protocol smart contract address in a specific chain. It is NOT possible for the identifiers to change over the lifetime of the contract once are mapped, except on the deletion. Therefore, the combination of the '(contract address, string tokenId, uint256 key, chain)' will be a globally unique and fully-qualified identifier for a specific asset on an EVM compatible chain. The 'tokenId' and 'key' should be treated as a "black box", and it is important to note that Signs can be destroyed/deleted by the functions included in the first standard Apeiron Protocol Cartridge, called "Pong". This happens because of a transfer or on a Console owner request.

The choice of `string` for the tokenId allows a wide variety of representation applications, that in conjuntion with the metadata implementation will bring infinite possibilities to the developers.

**Privacy**

This pre-draft is derived from the ERC-721 proposal as 'template'. As such, this section of this document might appear to be out of context or incomplete. We acknowledge these potential inconsistencies and are actively learning and working towards refining this draft.

**Security**

We have analyzed many potential threats to the protocol, and we know there is more. Here is a list of the detected ones and the solution implemented.

- Proxy Smart Contract administration and functions
We explored various alternatives, including the Transparent Proxy by OpenZeppelin. However, this excellent solution wasn't practical for Apeiron Protocol since the contract administrator would need continuous interaction with it. Therefore, our resolution involved defining an admin function for the Proxy smart contract and securing it, adding 'EIP-1967: Proxy Storage Slots' policies to prevent Storage collisions. It's crucial to never accept a Cartridge as compliant if it contains functions that conflict with the Console functions, based on the first four bytes of the function selector. If such a conflict occurs, the Cartridge functions selectors must be modified. Also the fallback functions should be dissabled.
At one point we contemplated utilizing Libraries instead of Execution smart contracts for the Cartridges. However, we concluded that not only does this approach make rapid development more challenging, but Libraries also pose a significant risk to the integrity of the Proxy smart contract tokens mapping. This integrity is paramount, and Libraries could potentially manipulate functions in unforeseen ways.

- Protocol Compliance
By the EXTCODEHASH revision we ensure, in the local Proxy smart contract (Console), that the code of the remote Console is as expected. Initially, and until protocol updates occur, this will remain the same, but there could be versions in the future. Therefore, this guarantees that in addition to ensuring safe storage management and preserving the integrity of the tokens, there isn't a function that obscures or tries to overwrite the value of the function where the Execution contract (Cartridge) is defined in the Console. Thus, we can confidently state that by reading the remote value of this variable, we will obtain the address of the Execution smart contract that the remote Proxy is using, and we can continue with the compliance review of it (Cartridge).
When the first check is completed and positive, we proceed to verify the EXTCODEHASH of the remote Execution contract (Cartridge). If it's a Cartridge we want to interact with, by matching it against the whitelist where initially there is only one, we procceed, but if it's not on the whitelist, the transaction will be reverted. This is what we call "the grid" where the tokens can freely move: All the compliance Consoles/Cartridges, and you can go in and out of the grid, or move between them, as your will.
It is not recommended to change these values or add EXTCODEHASH to the whitelists of Consoles or Cartridges unless we clearly understand what we are doing. This can potentially lead to theft and/or deletion of our tokens, involvement in the sending or production of unwanted or counterfeit tokens, substitution of metadata, or even total loss of our Console and therefore all tokens contained within it
It's important to note that, thanks to the possible redundancy added between the tokenId and metadata (in addition to ownership data, smart contracts, and chains), it's likely that many (or perhaps all) stolen tokens or the contract in its entirety could lose all on-chain/off-chain value when an attack occurs. These could potentially be regenerated, possibly with the help of Validators who signed the metadata, if they used more variables than the tokenId, and some effort from the victim to process the recoveries. Ultimately, this could restore all their value, leaving the stolen ones worthless, without the need to create blacklists, request help from uninvolved third parties, and thanks to the simple compliance that the protocol in general suggests.

- Token Redundancy
The token mapping is stored in the Console and is handled by 3 simple functions in the Console, accesible ONLY by the owner and the Cartridge. The tokenId and key value can't be modified after creation by anyone, including the own Console. The tokens can be deleted, just for remove them or when a transfer occurs. The metadata can be changed and updated as many times the user wants. With this concept we guarantee that there will be never a false token around, since the 'tokenId'+'key'+'contract_address'+'chain' can't never be duplicated, so the metadata will guarantee this for never happening.

- Token Security
User can choose with wich trusted Consoles (smart contract addresses) want's to interact. Not only because of the protocol protection that is guaranteed, but because he may be interested in only accept interaction with some developers, enterprises or at his will.

- SPAM
At contract level the owner_change proceess involves a push function (can be pulled) so the contract owner should add to a mapping the future contract owner address, then the counterpart should initialize the function and change the current contract owner to his address. At tokens level users can choose to block all incomming transaction (except whitelisted) so his contract will not accept any token by push requests.

- More is coming

**Optimization**

- Smart Contract constructors 'Parent' > 'Child.
To make it extremely simple for the users and mass adoption a Constructor will be available to deploy the Consoles with one transaction, then initializing and other 'new deployed contracts exploits' issues are also considered to avoid hacks in the deployment dApp or smart contract code, this needs revision. The Smart Contract will be deployed with the recommended configuration values, unless the user (expert mode) configures others, that can be changed later. Hackers will be ready to exploit them ASAP a new contract is deployed!

- Unique Cartridges
Everyone is free to deploy their compliance Cartridge/s but the recommendation is to use the ones that are already deployed since this will be exactly the same and will avoid you to pay deployment gas. There will be no difference between using the deployed one or a new deployment by the user since 'Pong' Cartridge (the first one) will not have any Storage. 
Note that other Cartridges can need specific user Storage, such as a minting one ('Asteroids') so this could requiere a new deployment (to be considered, not decided yet). Other situation will happen with Enterprise or community Cartridges, and they will decide if the their Cartridges (new EXTCODEHASHs) needs an extra data storage or their specific mapping requirements, so they can consider to push users to deploy it or not, depending their architecture, many ways to do this but is not our task to give the options here now.

- Optional Binary Compressed metadata with 'brotli'
In our mission to 'save gas', we found it interesting to explore a compression (not encryption) algorithm for TEXT inputs. Given the widespread adoption of base64 encoding for metadata with no reported issues, using 'brotli' not only aids in storage optimization but also allows us to change the metadata field to byte, which is more cost-effective to manage.
Given that a dApp or front end always processes metadata, using 'brotli' presents no more difficulty than decoding base64 text. We have considered potential challenges of this when metadata needs to be processed on-chain, although this is a situation that rarely occurs. If our understanding on this matter proves incorrect, we can revert this 'optimization'.
Furthermore, Console owners can choose whether or not to store the metadata of the tokens, leaving the 'metadata' at no value for the tokens. If the user chooses not to store the metadata, when a metadata request is made, the Console function will use the configured 'baseURI' and reconstruct the metadata URI for the given token as specified above in the 'interface'. This feature is intended for advanced users who opt to host their own metadata (we refer to it as web3 responsibility!)

- EXTCODEHASH Checks
There are other, more 'complex' and expensive yet 'easy to implement' options that we have decided to discard. One such option is a mapping in the Parent smart contract that stores each Console creation. However, after conducting a risk analysis, we discovered numerous potential exploits within our architecture. As a result, we concluded that the only way to be 100% sure of the party we are transacting with is by reviewing the EXTCODEHASH of the provided contract address and on this we set the Compliance requirement, which also happens to be a cost-effective transaction.
We urge caution when adding an EXTCODEHASH to the Console and Cartridges whitelists, as these actions require extensive audits and carry inherent risks. Each user must assume responsibility for these actions.

- More is coming

**Metadata Choices** (metadata extension)

A mechanism is provided to associate Signs with URIs. We expect that many implementations will take advantage of this to provide metadata for each Sign. The URI MAY be mutable (i.e. it changes from time to time). We considered a Sign representing ownership of a mansion, in this case metadata about the mansion (image, occupants, etc.) can naturally change.

The metadata is stored and returned as a byte value, compressed using 'brotli' algorithm, in the order to save gas on every transaction because the less use of transactional data and/or storage. Other tokens, like NFTs, stores the metadata using a string value: 'text plain' or 'base64' (even worst). Since this information is mainly used off-chain from the `web3` by dApps, NOT from other contracts, we find this change accurrate because we have not considered a use case where an on-chain application would query such information and need to use a decompress and decodification on a node, something that whould be very expensive.

The metadata URI will either be generated by the Console when the owner uploads their token metadata to their own storage, or it will be directly transferred from the Storage for each individual token in cases where the former is not applicable.

**Community Consensus**

Let's start the discussion!!!

We were at ETHBogota 2022, DEVCON VI, ETHSan Francisco 2022 and ETHDenver 2023,  where we discussed our project to create distinguishable asset standards. We invite you to review our project notes which will be published soon, please visit our Twitter account https://twitter.com/EIP1492 for more information and updates in the meanwhile this is not published for Draft review.

We have made an effort to be inclusive in this process and welcome anyone with questions or contributions to join our discussion. However, please note that this standard is specifically designed to support NOT ONLY the identified use cases listed herein.

## Backwards Compatibility

This pre-draft is derived from the ERC-721 proposal as 'template'. As such, this section of this document might appear to be out of context or incomplete. We acknowledge these potential inconsistencies and are actively learning and working towards refining this draft.

## References

**Standards**

This pre-draft is derived from the ERC-721 proposal as 'template'. As such, this section of this document might appear to be out of context or incomplete. We acknowledge these potential inconsistencies and are actively learning and working towards refining this draft.

1. [EIP-1967](https://github.com/ethereum/EIPs/issues/1967) Proxy Storage Slots.

## Use-cases

**Under development**
- Social Network [arcoIRIS](https://app.buidlbox.io/projects/arcoiris) self-sovereign, decentralized and multi-chain social network dApp.
- Real Estate

**Become a early use-case**

- DeFi
- GameFi
- Art Collections
- Books
- Music
- Licences
- Token Marketplce
- POAPs
- Ticketing
- E-commerce
- Rentals

## Copyright

OPEN SOURCE MIT LICENCED PROJECT cc0 & everything that states that this is totally free.
