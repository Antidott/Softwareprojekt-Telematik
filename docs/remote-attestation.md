# Remote attestaion
This Markdownfile shows you how we implemented the remote attestion with the PoC of the key managment.

_**Note**: the below commands can be send to zone 2 with the pattern `send 2 <command>`, e.g. `send 2 get_sp_key`._

## How we implemented ```get_sp_key```.
We programmed the remote attestation based on the PoC from Anton's work. Since Anton's programmed attestation does not have the logic of the key management, we had extended it with ```get_sp_key``` and ```attest_nonce```. At first in the command ```get_sp_key``` the function ```spongent-kdf();```generates the ```sp_key```, which will used further in ```attest_nonce```. For generates the ```sp_key```, the command needs the default_key and the randomly generated integer ```sp_id```, which we transform into a ```void *```. The calculation happens with the function ```spongent-kdf();```, which saves the result in ```sp_key```. We then send this key with the function ```print_key``` to zone 1, which displays it in the terminal.

## How we implemented ```attest_nonce```.
In ```attest_nonce``` the rest of the key management is executed. First, according to the logic, the request with the ```nonce number``` must be received and then the ```.text section``` must be read from the shared memory, which will be attested. A hash is then created from the ```.text section``` by the command ```spongent_hash();``` and stored in the Variable ```sm_id```. Then the  ```sm_key``` will be generated with the function ```spongent_kdf();```, which need the ```sm_id``` and the ```sp_key```, and store it in the Variable ```sm_key```. In order for ```attest_nonce``` to access ```sp_key```, ```sp_key``` must be global. After the calculation of ```sm_key```, a ```mac``` will be calculated with the function ```spongent_mac();```, which need a```sm_key``` and a ```message```, in response to the ```nonce number``` request. The calculated ```mac``` is stored in a Variable ```mac``` and sent to zone 1 with ```print_mac```, which displayed the ```mac``` in the terminal.  

_**Note**: the `attest_nonce` command has now been renamed to just `nonce=<nonce>`, so it can be invoked like `send 2 nonce=1234`._
