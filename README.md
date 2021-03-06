# ERC721
Scaleable ERC-721 Token based on standard found [here](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-721.md). 

The "standard" directory contains interfaces for relevant standards.
The mutability and visibility of some functions in the interface files have been modified. This is done in accordance with the standard, and does not affect the function signatures, nor the interfaceIDs.
The "libraries" directory contains required libraries, such as "SafeMath.sol" for preventing sneaky business with overflows and such.

TokenERC721.sol adheres to only the most basic of the standards, that being in ERC721.sol. It is fully scalable, but tokens have no metadata. The only parameter the constructor takes is the total number of tokens. For scalability, tokenIds start at 1, and increment by 1.
Initially, the contract creator owns all tokens in the contract. The tokenIds don't start at 0 is because 0 is the default value of ints, which I plan to take advantage of in the future (by using the 0 val as invalid).
When tokens are created during contract deployment (with initialTokens), it's fully scalable because it costs the same to create 1 as it does to create 10,000. There is also an optional public function, only callable by the contract creator for issuing more tokens, but in order to conform to the event emitting requirements, this function is slightly less scalable because it needs to emit an event for each token that gets created (so gas cost is linearly proportional to number of tokens being minted).

TokenERC721Metadata.sol extends TokenERC721.sol and adheres both to the basic 721 standard, and also the ERC721Metadata.sol standard, which gives the token contract a name and symbol (as with the ERC20 standard), as well as giving each token a URI for a file which contains metadata for the token. 

In order to keep the contract scalable, the metadata contract has a "uriBase" parameter, which is a string (stored as bytes). When the tokenURI function is called, the contract returns a string which is the uriBase concatenated with the tokenId. This means each token's URI doesn't need to be manually defined. It's assumed that the URI base will take the form of "something.com/" with the trailing "/", but it's not required so long as the concatenated form resolves to a file with the tokens metadata.

TokenERC721Enumerable.sol extends TokenERC721.sol aswell, and adheres to basic 721 and also the ERC721Enumerable.sol standard, which allows token lookup via index aswell as token ID.

This addition is not as scaleable. In order to satisfy the following criteria:
1. tokens can be minted in batches, 
2. all tokens are uniquely meaningful from the point of minting,
3. contract creator owns tokens when they are minted

it was necessary to iterate through newly minted tokens to add them to relevant index arrays. This means gas cost increases linearly with the number of tokens (during both contract creation and using the optional issueTokens function). However, it also means that all other operations still have a fixed cost, which was seen as more important. It is an assumption, but presumably newly minted NFTs will have a value >> the cost of minting the token. So in this regard it still scales.

TokenERC721Full.sol is simply a contract that combines both the extensions. There's no extra code, just a constructor that calls the constructors of the two extensions. It'll give you a compiler warning because both extensions call the constructor of the main one... but meh.
# erc721-master
# erc721-master
