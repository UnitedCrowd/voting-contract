![UnitedCrowd](https://staging.unitedcrowd.com/github/uc-logo.jpg)
# voting contract
The voting contract is a ethereum ERC20 based smart contract designed to costumize votings and polls. The contract can be included in any sale-based smart contract in order to give tokenholder the right to vote. An external DAPP can be added to setup votig contract.
## Function list
### Basic functions
Function | Description
---------|------------
contract Voting is CustomOwnable | Constructor: Initlizes poll, determines the current state of a poll, defines voter and tokenmanager
createPoll | creates a new poll
endPoll | Ends a poll. Only the creator of a given poll can end that poll.
getPollStatus | Returns the status of a poll.
getPollExpirationTime | Gets the expiration date of a poll.
getPollHistory | Gets the complete list of polls a user has voted in.
getPollInfoForVoter | Gets a voter's vote and weight for a given expired poll.
getVotersForPoll | Gets all the voters of a poll.
getLockedAmount | Gets the amount of Voting Tokens that are locked for a given voter.
getTokenStake | Gets the amount of Voting Credits for a given voter.
getIfUserHasVoted | Checks if a user has voted for a specific poll.
modifier validPoll | Modifier that checks for a valid poll ID.

### Vote Operations
Function | Description
---------|------------
castVote | Casts a vote for a given poll.

### Token Operations
_NOTE: User must approve transfer of tokens. weight is denominated in wei_
Function | Description
---------|------------
stakeVotingTokens | Stakes tokens for a given voter in return for voting credits.
withdrawTokens | Allows a voter to withdraw voting tokens after a poll has ended.
updateTokenBank | Helper function that updates active token balances after a poll has ended.


