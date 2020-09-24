# sendMyVote.sh | How to Use It

sendMyVote.sh was created to be a simple, easy-to-use, all-in-one script to submit your generated voting JSON files to the blockchain as metadata to a 
transaction.

All credit for the logic in this script goes to Martin of ATADA Stakepool (Telegram: @atada_stakepool).

To use this script requires a total of four (4) files: the sendMyVote.sh script, a payment address, the secret key (skey) of the payment address, and a
properly formatted JSON file.

For maximum safety when submitting votes to the blockchain, we recommend using a "disposable" address with only enough funds to submit the transaction 
(usually 1.5-2 Ada should be more than enough). Transactions can be submitted from any node on the blockchain including "passive" relay nodes (no need
to expose your block producer or pledge address!).

## Step #1: Create a Directory to Hold Voting Files

```console
cd ~
mkdir vote
cd vote
```

## Step #2: Create a Voting Address

```console
cardano-cli shelley address key-gen \
--verification-key-file payment.vkey \
--signing-key-file payment.skey
```
The command above will create two new files in the current directory `payment.vkey` and `payment.skey` these are your signing keys for sending funds from the address.

In order to fund the newly created payment keys, we need to generate a payment address from the keys.

```console
cardano-cli shelley address build \
--payment-verification-key-file payment.vkey \
--out-file payment.addr \
--mainnet
```
This will create a new file in the current directory `payment.addr` which can be used to send funds from an existing wallet. You can view the address by typing:
```console
cat payment.addr
```

## Step #3: Fund the Address

As mentioned previously, you will now need to send a small amount of Ada to this address in order to pay the transaction fees for submitting your vote.

You can use the `cardano-cli` to send funds from another command line wallet or you can send funds from Daedalus or Yoroi. (Creating a simple transaction official documentation: https://docs.cardano.org/projects/cardano-node/en/latest/stake-pool-operations/simple_transaction.html)

Once you have sent funds, you can use `cardano-cli` to check the balance of your voting wallet to ensure you're ready to proceed

```console
cardano-cli shelley query utxo \
--address $(cat payment.addr)
--mainnet
```

## Step #4: Save Your JSON File to the Server

Save your properly formatted JSON file to your server to be submitted as metadata. I personally prefer to use `nano` and simply copy-and-paste the contents
of the JSON into the new file.

```console
nano ballot.json
<paste in your JSON>
<hit CTRL+X to close nano and save the file, follow on-screen prompts>
```

Once you've saved you can use `cat` to confirm that the file was saved correctly

```console
foo@bar:~/vote$ cat test.json
{
  "1": {
    "ObjectType": "VoteBallot",
    "ObjectVersion": "1.0.0",
    "NetworkId": "SPOCRA",
    "ProposalId": "ABC-123-ABC-123"
    "VoterId": "ABC-123-ABC-123"
    ... Other Ballot Data Here ...
  }
}
```

## Step #5: Get the `sendMyVote.sh` Script

If you haven't already, you'll need to download a copy of the `sendMyVote.sh` script to your local machine and ensure that it is an executable file.

```console
wget https://github.com/Crypto2099/cardano/raw/master/sendMyVote.sh
chmod +x sendMyVote.sh
```

## Step #6: Customize the `sendMyVote.sh` Script to Match Your Environment

You will need to edit the `sendMyVote.sh` script to make the variables at the top of the file match your particular operating environment. Simply open the
script in your text editor of choice (`nano sendMyVote.sh`) and change the following block to match your system.

```bash
###
# Change the following variables to match your configuration
###

CPATH="/home/<user>/cardano-node"
socket="${CPATH}/db/node.socket"
genesisfile="${CPATH}/config/testnet-shelley-genesis.json"
genesisfile_byron="${CPATH}/config/testnet-byron-genesis.json"
cardanocli="/home/<user>/.cabal/bin/cardano-cli"
cardanonode="/home/<user>/.cabal/bin/cardano-node"
byronToShelleyEpochs=208
magicparam="--mainnet"

###
# STOP EDITING!!!
###
```

## Step #7: Submit Your Ballot!!!

Okay, with all that said and done, the final step is to submit your ballot to the chain using the `sendMyVote.sh` script. The arguments to the script are
to provide the "wallet name" (without any file extension) and the JSON file name (without the .json extension) like shown below:

```console
foo@bar:~/vote$ ls
ballot.json payment.addr payment.skey payment.vkey sendMyVote.sh
foo@bar:~/vote$ ./sendMyVote.sh payment ballot
Using lovelaces from Address payment.addr to send the metafile ballot.json:

Current Slot-Height: 6535314 (setting TTL to 6545314)

Source/Destination Address payment.addr: addr1qpf9ppdq2uzz4qm63s6un5nxj0w49zpd0ehh3w7hprpr7ss9hvnq404ry253qp5e7ccmdpspklgpg7d90weh6g9j3elsks24c4
Attached Metafile: ballot.json

1 UTXOs found on the source Addr!

HASH: 1fca8532820d34ccf6fd61d01961e7b6f7f5f8a9fb7171d1165c929825dcca2a   INDEX: 0        LOVELACES: 3385978352706
Total lovelaces in UTX0:  3385978352706 lovelaces

Minimum Transaction Fee for 1x TxIn & 1x TxOut:  175005 lovelaces
Lovelaces to return to payment.addr:  3385978177701 lovelaces


Building the unsigned transaction body:  /tmp/payment.txbody

{
    "type": "TxUnsignedShelley",
    "description": "",
    "cborHex": "82a500818258201fca8532820d34ccf6fd61d01961e7b6f7f5f8a9fb7171d1165c929825dcca2a00018182583900525085
    a057042a837a8c35c9d26693dd52882d7e6f78bbd708c23f4205bb260abea322a9100699f631b68601b7d01479a57bb37d20b28e7f1b00
    0003145c06c8a5021a0002ab9d031a0063dfa2075820e43865d2f29b6d673893dad8efb616d2d67001f48e14f39d20095646be03a3a0a1
    01a36444617461781b4164616d2074657374696e6720737475666620616761696e2e2e2e6a4f626a6563745479706568546573744a534f
    4e6d4f626a65637456657273696f6e65312e302e30"
}

Sign the unsigned transaction body with the payment.skey:  /tmp/payment.tx

{
    "type": "TxSignedShelley",
    "description": "",
    "cborHex": "83a500818258201fca8532820d34ccf6fd61d01961e7b6f7f5f8a9fb7171d1165c929825dcca2a00018182583900525085
    a057042a837a8c35c9d26693dd52882d7e6f78bbd708c23f4205bb260abea322a9100699f631b68601b7d01479a57bb37d20b28e7f1b00
    0003145c06c8a5021a0002ab9d031a0063dfa2075820e43865d2f29b6d673893dad8efb616d2d67001f48e14f39d20095646be03a3a0a1
    00818258209e0a0595f9c0c7671c254bf569a460d1e73f204347848772c6c7ac54db04667658400d70762745a0ce86ab888165a0793cef
    7d85931a1713e39ad2b1eb3a0758c673c8ce22fa8747ba07d68532762af36f5edac3441b7e011db76578e818e51a5f0ca101a364446174
    61781b4164616d2074657374696e6720737475666620616761696e2e2e2e6a4f626a6563745479706568546573744a534f4e6d4f626a65
    637456657273696f6e65312e302e30"
}

Does this look good for you, continue ? [y/N]
```

Simply press `Y` and the script will automatically submit the transaction to the blockchain for you!

If everything works correctly, you should see a message like shown below:

```console
Submitting the transaction via the node...DONE
```

**Important Note:** There is no "positive confirmation" when submitting a transaction to the blockchain. This means
you will not see anything but what is shown above on a success. However, if there is an error for any reason in the
transaction you will see the error message appear in your terminal.

### Help & Support
If you encounter any issues while using this script or submitting your ballot to the chain, please do not hesitate
to reach out to Adam Dean on the Cardano **Stake Pool Operators Collective Representation Assembly** (SPOCRA) Telegram
Channel: https://t.me/SPOCRA or Discord Channel: https://discord.gg/cCJRdgp
