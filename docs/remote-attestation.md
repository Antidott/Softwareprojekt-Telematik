# Remote attestaion
This Markdownfile shows you how we implemented the remote attestion with the PoC of the key managment.

## How we programmed ```get_sp_key```.
We programmed the attestation based on the PoC from Anton's work. Since Anton's programmed attestation does not have the logic of the key management, we had extended it with ```get_sp_key``` and ```attest_nonce```. The cmd_command get_sp_key generates the ```sp_key```. For this, the command needs the default_key and the randomly generated integer ```sp_id```, which we transform into a ```void *```. The calculation happens with the function ```spongent-kdf();```, which saves the result in ```sp_key```. We then send this key with the function ```print_key``` to zone 1, which displays it.

## How we programmed ```attest_nonce```.
