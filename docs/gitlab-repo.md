# GitLab Repo

All of our code lives in the Uni-intern GitLab at <https://git.imp.fu-berlin.de/elef42/attestation/>.
This is a fork of the original [Bachelorthesis] code, where each of our additions/ changes are pushed to separate branches.
We have a couple of WIP branches with non-linear history, that are a bit confusing. 
To ease understanding, we rewrote the git history in new, separate branches for each milestone and wip-feature.
The naming matches with the milestones in this Repo.
Sub-features that are still WIP are done on sub-branches for each milestone.

The following branches are relevant (subbranches are named as <parent_name>_<subbranch_name>):

- `feat/support-X300`: Changes in the memory-config and linker script for our board (X300)
  - This just ports the project to our board, since before the only supported target was the FE310 Board
  - All other branches are based on this branch!
- `milestone3`: Continued work to fully implement the attestation, as described in [Milestone 3](https://github.com/Antidott/Softwareprojekt-Telematik/milestone/3)
  - Currently not working properly: merging the two below sub-branches resulted in the `attest_nonce` command not working.
- `milestone3_sp-key`: Deriving a software-provider key and providing a command to return it. See [Issue #7](https://github.com/Antidott/Softwareprojekt-Telematik/issues/7).
- `milestone3_attest-nonce`: Command to calculate MAC for a nonce, based on the shared buffer content. See [Issue #6](https://github.com/Antidott/Softwareprojekt-Telematik/issues/6).
- `milestone4`: Continued work to implement secure software updates, as described in [Milestone 4](https://github.com/Antidott/Softwareprojekt-Telematik/milestone/4)
  - Don't let zone 1 write to shared buffer, instead have the update send as a message to zone 4
- `milestone4_use-interrupt`: Use interrupts in zone 4 to handle messages
  - Allows to run the user binary without returning. Update user application by updating the shared buffer and then resetting the saved Program-Counter when in interrupt mode. See [Issue #9](https://github.com/Antidott/Softwareprojekt-Telematik/issues/9).
  - Updating a full binary not tested yet

The following branches were our work-in-progress branches for the different features, but the history is a bit messy due to a lot of merging between the branches:

- `feat/get-functionality`: Working branch for the `get_sp_key` feature
- `feat/full-attestation`: Working branch for the `attest_nonce` feature
- `feat/sercureSWU`: Working branch for secure software updates
- `feat/SWU-whole-binary`: Parallel working branch to `feat/secureSWU`
  - couple of changes to run a user app and us interrupt handler for message handling

[Bachelorthesis]: ./RA-on-Multzone_notes.md