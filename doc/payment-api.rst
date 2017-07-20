Zcash Payment API
=================
Overview
---------

Zcash extends the Bitcoin Core API with new RPC calls to support private Zcash payments.

Zcash payments make use of two address formats:

taddr
  an address for transparent funds (just like a Bitcoin address, value stored in UTXOs)

zaddr
  an address for private funds (value stored in objects called notes)

When transferring funds from one taddr to another taddr, you can use either the existing Bitcoin RPC calls or the new Zcash RPC calls.

When a transfer involves zaddrs, you must use the new Zcash RPC calls.


Compatibility with Bitcoin Core
-------------------------------

Zcash supports all commands in the Bitcoin Core API (as of version 0.11.2).   Where applicable, Zcash will extend commands in a backwards-compatible way to enable additional functionality.

We do not recommend use of accounts which are now deprecated in Bitcoin Core.  Where the account parameter exists in the API, please use “” as its value, otherwise an error will be returned.

To support multiple users in a single node’s wallet, consider using ``getnewaddress`` or ``z_getnewaddress`` to obtain a new address for each user.  Also consider mapping multiple addresses to each user.

List of Zcash API commands
--------------------------

Optional parameters are denoted in [square brackets].

RPC calls by category:

* Accounting: ``z_getbalance``, ``z_gettotalbalance``
* Addresses : ``z_getnewaddress``, ``z_listaddresses``, ``z_validateaddress``
* Keys : ``z_exportkey``, ``z_importkey``, ``z_exportwallet``, ``z_importwallet``
* Operation: ``z_getoperationresult``, ``z_getoperationstatus``, ``z_listoperationids``
* Payment : ``z_listreceivedbyaddress``, ``z_sendmany``

RPC parameter conventions:

taddr
  Transparent address
zaddr
  Private address
address
  Accepts both private and transparent addresses.
amount
  JSON format double-precision number with 1 ZC expressed as 1.00000000.
memo
  Metadata expressed in hexadecimal format.  Limited to 512 bytes,
  the current size of the memo field of a private transaction.  Zero
  padding is automatic.

Accounting
~~~~~~~~~~
+-----------------------+---------------------+--------------------------------------------------------+
| |c|                   | |p|                 | |d|                                                    |
+=======================+=====================+========================================================+
| ``z_getbalance``      | address [minconf=1] | | Returns the balance of a taddr or zaddr belonging to |
|                       |                     | | the node’s wallet. Optionally set the minimum number |
|                       |                     | | of confirmations a private or transaction must have  |
|                       |                     | | in order to be included in the balance. Use 0 to     |
|                       |                     | | count unconfirmed transactions.                      |
+-----------------------+---------------------+--------------------------------------------------------+
| ``z_gettotalbalance`` | [minconf=1]         | | Return the total value of funds stored in the node’s |
|                       |                     | | wallet. Optionally set the minimum number of         |
|                       |                     | | confirmations a private or transparent transaction   |
|                       |                     | | must have in order to be included in the balance.    |
|                       |                     | | Use 0 to count unconfirmed transactions.             |
|                       |                     | |                                                      |
|                       |                     | | Output:                                              |
|                       |                     |                                                        |
|                       |                     | .. parsed-literal::                                    |
|                       |                     |                                                        |
|                       |                     |    {                                                   |
|                       |                     |      "transparent" : 1.23,                             |
|                       |                     |      "private" : 4.56,                                 |
|                       |                     |      "total" : 5.79                                    |
|                       |                     |    }                                                   |
+-----------------------+---------------------+--------------------------------------------------------+
       
Addresses
~~~~~~~~~

+-----------------------+---------------------+--------------------------------------------------------+
| |c|                   | |p|                 | |d|                                                    |
+=======================+=====================+========================================================+
| ``z_getnewaddress``   |                     | | Return a new zaddr for sending and receiving         |
|                       |                     | | payments. The spending key for this zaddr will be    |
|                       |                     | | added to the node’s wallet.                          |
|                       |                     | |                                                      |
|                       |                     | | Output:                                              |
|                       |                     |                                                        |
|                       |                     | .. parsed-literal::                                    |
|                       |                     |                                                        |
|                       |                     |    zc123...                                            |
+-----------------------+---------------------+--------------------------------------------------------+
| ``z_listaddresses``   |                     | | Returns a list of all the zaddrs in this node’s      |
|                       |                     | | wallet for which you have a spending key.            |
|                       |                     | |                                                      |
|                       |                     | | Output:                                              |
|                       |                     |                                                        |
|                       |                     | .. parsed-literal::                                    |
|                       |                     |                                                        |
|                       |                     |    {                                                   |
|                       |                     |      [“zc123...”, “zc456...”, “zc789...”]              |
|                       |                     |    }                                                   |
+-----------------------+---------------------+--------------------------------------------------------+
| ``z_validateaddress`` | z-address           | | Return information about a given zaddr.              |
|                       |                     | |                                                      |
|                       |                     | | Output:                                              |
|                       |                     |                                                        |
|                       |                     | .. parsed-literal::                                    |
|                       |                     |                                                        |
|                       |                     |    {                                                   |
|                       |                     |      "isvalid" : true,                                 |
|                       |                     |      "address" : "zc123...",                           |
|                       |                     |      "payingkey" : "f5bb3c...",                        |
|                       |                     |      "transmissionkey" : "7a58c7...",                  |
|                       |                     |      "ismine" : true                                   |
|                       |                     |    }                                                   |
+-----------------------+---------------------+--------------------------------------------------------+

Key Management
~~~~~~~~~~~~~~~

+-----------------------+---------------------+--------------------------------------------------------+
| |c|                   | |p|                 | |d|                                                    |
+=======================+=====================+========================================================+
| ``z_exportkey``       | z-address           | | Return a  private key for a given zaddr belonging to |
|                       |                     | | the node’s wallet.                                   |
|                       |                     | |                                                      |
|                       |                     | | The key will be returned as a string formatted using |
|                       |                     | | Base58Check as described in the Zcash protocol spec. |
|                       |                     | |                                                      |
|                       |                     | | Output:                                              |
|                       |                     |                                                        |
|                       |                     | .. parsed-literal::                                    |
|                       |                     |                                                        |
|                       |                     |    SKxyN...                                            |
+-----------------------+---------------------+--------------------------------------------------------+
| ``z_importkey``       | zkey [rescan=true]  | | Add a private key as returned by z_exportkey to a    |
|                       |                     | | node's wallet.                                       |
|                       |                     | |                                                      |
|                       |                     | | The key should be formatted using Base58Check as     |
|                       |                     | | described in the Zcash protocol spec.                |
|                       |                     | |                                                      |
|                       |                     | | Set rescan to true (the default) to rescan the       |
|                       |                     | | entire local block database for transactions         |
|                       |                     | | affecting any address or pubkey script in the wallet |
|                       |                     | | (including transactions affecting the newly-added    |
|                       |                     | | address for this spending key).                      |
+-----------------------+---------------------+--------------------------------------------------------+
| ``z_exportwallet``    | filename            | | Creates or overwrites a file with taddr private keys |
|                       |                     | | and zaddr private keys in a human-readable format.   |
|                       |                     | |                                                      |
|                       |                     | | Filename is the file in which the wallet dump will   |
|                       |                     | | be placed. An existing file with that name will be   |
|                       |                     | | overwritten. File is located in the export directory | 
|                       |                     | | set with:                                            |
|                       |                     |                                                        |
|                       |                     | .. parsed-literal::                                    |
|                       |                     |                                                        |
|                       |                     |    exportdir=/path/to/chosen/export/directory          |
|                       |                     |                                                        |
|                       |                     | | in ``zcash.conf``.                                   |
|                       |                     | |                                                      |
|                       |                     | | No value is returned but a JSON-RPC error will be    |
|                       |                     | | reported if a failure occurred.                      |
+-----------------------+---------------------+--------------------------------------------------------+
| ``z_importwallet``    | filename            | | Imports private keys from a file in wallet export    |
|                       |                     | | file format (see ``z_exportwallet``). These keys     |
|                       |                     | | will be added to the keys currently in the wallet.   |
|                       |                     | | This call may need to rescan all or parts of the     |
|                       |                     | | blockchain for transactions affecting the newly      |
|                       |                     | | added keys, which may take several minutes.          | 
|                       |                     | |                                                      |
|                       |                     | | Filename is the file to import located in the export | 
|                       |                     | | directory set with:                                  |
|                       |                     |                                                        |
|                       |                     | .. parsed-literal::                                    |
|                       |                     |                                                        |
|                       |                     |    exportdir=/path/to/chosen/export/directory          |
|                       |                     |                                                        |
|                       |                     | | in ``zcash.conf``.                                   |
|                       |                     | |                                                      |
|                       |                     | | No value is returned but a JSON-RPC error will be    |
|                       |                     | | reported if a failure occurred.                      |
+-----------------------+---------------------+--------------------------------------------------------+


### Payment

Accounting
~~~~~~~~~~
+-----------------------------+----------------------------------------------+------------------------------------------+
| |c|                         | |p|                                          | |d|                                      |
+=============================+==============================================+==========================================+
| ``z_listreceivedbyaddress`` | z-address [minconf=1]                        | | Return a list of amounts received by a |
|                             |                                              | | z-address belonging to the wallet.     |
|                             |                                              | |                                        |
|                             |                                              | | Optionally set the minimum number of   |
|                             |                                              | | confirmations which a received amount  |
|                             |                                              | | must have in order to be included in   |
|                             |                                              | | the result.  Use 0 to count            |
|                             |                                              | | unconfirmed transactions.              |
|                             |                                              | |                                        |
|                             |                                              | | Output:                                |
|                             |                                              |                                          |
|                             |                                              | .. parsed-literal::                      |
|                             |                                              |                                          |
|                             |                                              |    [                                     |
|                             |                                              |      {“txid”: “4a0f…”,                   |
|                             |                                              |       “amount”: 0.54,                    |
|                             |                                              |       “memo”:”F0FF…”,},                  |
|                             |                                              |       {...},                             |
|                             |                                              |       {...}                              |
|                             |                                              |    ]                                     |
+-----------------------------+----------------------------------------------+------------------------------------------+
| ``z_sendmany``              | fromaddress amounts [minconf=1] [fee=0.0001] | | *This is an Asynchronous RPC call*     |
|                             |                                              | |                                        |
|                             |                                              | | Send funds from an address to multiple |
|                             |                                              | | outputs.  The fromaddress can be       |
|                             |                                              | | either a t-address or z-address.       |
|                             |                                              | |                                        |
|                             |                                              | | Amounts is a list containing key/value |
|                             |                                              | | pairs corresponding to the addresses & |
|                             |                                              | | amount to pay. Each output address can |
|                             |                                              | | be a t-address or z-address.           |
|                             |                                              | |                                        |
|                             |                                              | | When sending to a zaddr, you also have |
|                             |                                              | | the option of attaching a memo in      |
|                             |                                              | | hexadecimal format.                    |
|                             |                                              | |                                        |
|                             |                                              | | **NOTE:** When sending coinbase funds  |
|                             |                                              | | to a zaddr, the node's wallet does not |
|                             |                                              | | allow any change. Put another way,     |
|                             |                                              | | spending a partial amount of a         |
|                             |                                              | | coinbase utxo is not allowed. This is  |
|                             |                                              | | not a consensus rule but a local       |
|                             |                                              | | wallet rule due to the current         |
|                             |                                              | | implementation of z_sendmany. In the   |
|                             |                                              | | future, this rule may be removed.      |
|                             |                                              | |                                        |
|                             |                                              | |                                        |
|                             |                                              | | Example of Outputs parameter:          |
|                             |                                              |                                          |
|                             |                                              | .. parsed-literal::                      |
|                             |                                              |                                          |
|                             |                                              |    [{“address”:”t123…”, “amount”:0.005}, |
|                             |                                              |     {“address”:”z010…”,”amount”:0.03,    |
|                             |                                              |      “memo”:”f508af…”}]                  |
|                             |                                              |                                          |
|                             |                                              | | Optionally set the minimum number of   |
|                             |                                              | | confirmations which a private or       |
|                             |                                              | | transparent transaction must have in   |
|                             |                                              | | order to be used as an input.          |
|                             |                                              | |                                        |
|                             |                                              | | Optionally set a transaction fee,      |
|                             |                                              | | which by default is 0.0001 ZEC.        |
|                             |                                              | |                                        |
|                             |                                              | | Any transparent change will be sent to |
|                             |                                              | | a new transparent address. Any private |
|                             |                                              | | change will be sent back to the zaddr  |
|                             |                                              | | being used as the source of funds.     |
|                             |                                              | |                                        |
|                             |                                              | | Returns an operationid. You use the    |
|                             |                                              | | ``operationid`` value with             |
|                             |                                              | | ``z_getoperationstatus`` and           |
|                             |                                              | | ``z_getoperationresult`` to obtain the |
|                             |                                              | | result of sending funds, which if      |
|                             |                                              | | successful, will be a txid.            |
+-----------------------------+----------------------------------------------+------------------------------------------+

Operations
~~~~~~~~~~

Asynchronous calls return an OperationStatus object which is a JSON object with the following defined key-value pairs:

* `operationid` : unique identifier for the async operation. Use this value with ``z_getoperationstatus`` or ``z_getoperationresult`` to poll and query the operation and obtain its result.
* `status` : current status of operation
    
  * `queued` : operation is pending execution
  * `executing` : operation is currently being executed
  * `cancelled`
  * `failed`
  * `success`
* `result` : result object if the status is `success`.  The exact form of the result object is dependent on the call itself.
* `error` : error object if the status is `failed`. The error object has the following key-value pairs:
    
  * `code` : number
  * `message` : error message

Depending on the type of asynchronous call, there may be other key-value pairs.  For example, a ``z_sendmany`` operation will also include the following in an OperationStatus object:

* `method` : name of operation e.g. ``z_sendmany``
* `params` : an object containing the parameters to ``z_sendmany``

Currently, as soon as you retrieve the operation status for an operation which has finished, that is it has either succeeded, failed, or been cancelled, the operation and any associated information is removed.

It is currently not possible to cancel operations.

+--------------------------+----------------+--------------------------------------------------------+
| |c|                      | |p|            | |d|                                                    |
+==========================+================+========================================================+
| ``z_getoperationresult`` | [operationids] | | Return OperationStatus JSON objects for all          |
|                          |                | | completed operations the node is currently aware of, |
|                          |                | | and then remove the operation from memory.           |
|                          |                | |                                                      |
|                          |                | | Operationids is an optional array to filter which    |
|                          |                | | operations you want to receive status objects for.   |
|                          |                | |                                                      |
|                          |                | | Output is a list of operation status objects, where  |
|                          |                | | the status is either `failed`, `cancelled` or        |
|                          |                | | `success`.                                           |
|                          |                |                                                        |
|                          |                | .. parsed-literal::                                    |
|                          |                |                                                        |
|                          |                |    [                                                   |
|                          |                |     {“operationid”: “opid-11ee…”,                      |
|                          |                |      “status”: “cancelled”},                           |
|                          |                |     {“operationid”: “opid-9876”, “status”: ”failed”},  |
|                          |                |     {                                                  |
|                          |                |      “operationid”: “opid-0e0e”,                       |
|                          |                |      “status”:”success”,                               | 
|                          |                |      “execution_time”:”25”,                            |
|                          |                |      “result”: {“txid”:”af3887654…”,...}               |
|                          |                |     }                                                  |
|                          |                |    ]                                                   |
+-----------------------+---------------------+------------------------------------------------------+
| ``z_getoperationstatus`` | [operationids] | | Return OperationStatus JSON objects for all          |
|                          |                | | operations the node is currently aware of.           |
|                          |                | |                                                      |
|                          |                | | Operationids is an optional array to filter which    |
|                          |                | | operations you want to receive status objects for.   |
|                          |                | |                                                      |
|                          |                | | Output is a list of operation status objects.        |
|                          |                |                                                        |
|                          |                | .. parsed-literal::                                    |
|                          |                |                                                        |
|                          |                |    [                                                   |
|                          |                |     {“operationid”: “opid-12ee…”,                      |
|                          |                |      “status”: “queued”},                              |
|                          |                |     {“operationid”: “opd-098a…”,                       |
|                          |                |      “status”: ”executing”},                           |
|                          |                |     {“operationid”: “opid-9876”,                       |
|                          |                |      “status”: ”failed”}                               |
|                          |                |    ]                                                   |
|                          |                |                                                        |
|                          |                | | When the operation succeeds, the status object will  |
|                          |                | | also include the result.                             |
|                          |                |                                                        |
|                          |                | .. parsed-literal::                                    |
|                          |                |                                                        |
|                          |                |    {                                                   |
|                          |                |     “operationid”: “opid-0e0e”,                        |
|                          |                |     “status”:”success”,                                |
|                          |                |     “execution_time”:”25”,                             |
|                          |                |     “result”: {“txid”:”af3887654…”,...}                |
|                          |                |    }                                                   |
+--------------------------+----------------+--------------------------------------------------------+
| ``z_listoperationids``   | [state]        | | Return a list of operationids for all operations     |
|                          |                | | which the node is currently aware of.                |
|                          |                | |                                                      |
|                          |                | | State is an optional string parameter to filter the  |
|                          |                | | operations you want listed by their state.           |
|                          |                | | Acceptable parameter values are `queued`,            |
|                          |                | | `executing`, `success`, `failed`, `cancelled`.       |
|                          |                | |                                                      | 
|                          |                | | Output:                                              |
|                          |                |                                                        |
|                          |                | .. parsed-literal::                                    |
|                          |                |                                                        |
|                          |                |    [“opid-0e0e…”, “opid-1af4…”, … ]                    |
+--------------------------+----------------+--------------------------------------------------------+

Asynchronous RPC call Error Codes
---------------------------------

Zcash error codes are defined in https://github.com/zcash/zcash/blob/master/src/rpcprotocol.h

``z_sendmany`` error codes
~~~~~~~~~~~~~~~~~~~~~~~~~~

+-----------------------------------------------+--------------------------------------------------------------+
| RPC_INVALID_PARAMETER (-8)                    | *Invalid, missing or duplicate parameter*                    |
+===============================================+==============================================================+
| "Minconf cannot be negative"                  | Cannot accept negative minimum confirmation number.          |
+-----------------------------------------------+--------------------------------------------------------------+
| | "Minimum number of confirmations cannot be  | Cannot accept negative minimum confirmation number.          |
| | less than 0"                                |                                                              |
+-----------------------------------------------+--------------------------------------------------------------+
| "From address parameter missing"              | Missing an address to send funds from.                       |
+-----------------------------------------------+--------------------------------------------------------------+
| "No recipients"                               | Missing recipient addresses.                                 |
+-----------------------------------------------+--------------------------------------------------------------+
| "Memo must be in hexadecimal format"          | Encrypted memo field data must be in hexadecimal format.     |
+-----------------------------------------------+--------------------------------------------------------------+
| | "Memo size of __ is too big, maximum        | Encrypted memo field data exceeds maximum size of 512 bytes. |
| | allowed is __ "                             |                                                              |
+-----------------------------------------------+--------------------------------------------------------------+
| | "From address does not belong to this node, | Sender address spending key not found.                       |
| | zaddr spending key not found."              |                                                              |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, expected object"          | Expected object.                                             |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, unknown key: __"          | Unknown key.                                                 |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, expected valid size"      | Invalid size.                                                |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, expected hex txid"        | Invalid txid.                                                |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, vout must be positive"    | Invalid vout.                                                |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, duplicated address"       | Address is duplicated.                                       |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, amounts array is empty"   | Amounts array is empty.                                      |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, unknown key"              | Key not found.                                               |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, unknown address format"   | Unknown address format.                                      |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, size of memo"             | Invalid memo field size.                                     |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, amount must be positive"  | Invalid or negative amount.                                  |
+-----------------------------------------------+--------------------------------------------------------------+
| "Invalid parameter, too many zaddr outputs"   | z_address outputs exceed maximum allowed.                    |
+-----------------------------------------------+--------------------------------------------------------------+
| |"Invalid parameter, expected memo data in    | Encrypted memo field is not in hexadecimal format.           |
| | hexadecimal format"                         |                                                              |
+-----------------------------------------------+--------------------------------------------------------------+
| | "Invalid parameter, size of memo is larger  | Encrypted memo field data exceeds maximum size of 512 bytes. |
| | than maximum allowed __ "                   |                                                              |
+-----------------------------------------------+--------------------------------------------------------------+


+---------------------------------------------------------+----------------------------------------+
| RPC_INVALID_ADDRESS_OR_KEY (-5)                         | *Invalid address or key*               |
+=========================================================+========================================+
| "Invalid from address, no spending key found for zaddr" | z-address spending key not found.      |
+---------------------------------------------------------+----------------------------------------+
| "Invalid output address, not a valid taddr."            | Transparent output address is invalid. |
+---------------------------------------------------------+----------------------------------------+
| "Invalid from address, should be a taddr or zaddr."     | Sender address is invalid.             |
+---------------------------------------------------------+----------------------------------------+
| | "From address does not belong to this node,           | Sender address spending key not found. |
| | zaddr spending key not found."                        |                                        |
+---------------------------------------------------------+----------------------------------------+


+--------------------------------------------------------------+------------------------------------------------+
| RPC_WALLET_INSUFFICIENT_FUNDS (-6)                           | *Not enough funds in wallet or account*        |
+==============================================================+================================================+
| "Insufficient funds, no UTXOs found for taddr from address." | Insufficient funds for sending address.        |
+--------------------------------------------------------------+------------------------------------------------+
| | "Could not find any non-coinbase UTXOs to spend. Coinbase  | Must send Coinbase UTXO to a single z_address. |
| |  UTXOs can only be sent to a single zaddr recipient."      |                                                |
+--------------------------------------------------------------+------------------------------------------------+
| "Could not find any non-coinbase UTXOs to spend."            | No available non-coinbase UTXOs.               |
+--------------------------------------------------------------+------------------------------------------------+
| | "Insufficient funds, no unspent notes found for zaddr from | Insufficient funds for sending address.        |
| | address."                                                  |                                                |
+--------------------------------------------------------------+------------------------------------------------+
| | "Insufficient transparent funds, have __, need __ plus     | Insufficient funds from transparent address.   |
| | fee __"                                                    |                                                |
+--------------------------------------------------------------+------------------------------------------------+
| "Insufficient protected funds, have __, need __ plus fee __" | Insufficient funds from shielded address.      |
+--------------------------------------------------------------+------------------------------------------------+


+----------------------------------------------+--------------------------------------------+
| RPC_WALLET_ERROR (-4)                        | *Unspecified problem with wallet*          |
+==============================================+============================================+
| "Could not find previous JoinSplit anchor"   | Try restarting node with ``-reindex``.     |
+----------------------------------------------+--------------------------------------------+
| | "Error decrypting output note of previous  |                                            |
| | JoinSplit: __"                             |                                            |
+----------------------------------------------+--------------------------------------------+
| "Could not find witness for note commitment" | Try restarting node with ``-rescan``.      |
+----------------------------------------------+--------------------------------------------+
| "Witness for note commitment is null"        | Missing witness for note commitement.      |
+----------------------------------------------+--------------------------------------------+
| | "Witness for spendable note does not have  | Invalid anchor for spendable note witness. |
| | same anchor as change input"               |                                            |
+----------------------------------------------+--------------------------------------------+
| "Not enough funds to pay miners fee"         | Retry with sufficient funds.               |
+----------------------------------------------+--------------------------------------------+
| "Missing hex data for raw transaction"       | Raw transaction data is null.              |
+----------------------------------------------+--------------------------------------------+
| "Missing hex data for signed transaction"    | Hex value for signed transaction is null.  |
+----------------------------------------------+--------------------------------------------+
| | "Send raw transaction did not return an    |                                            |
| | error or a txid."                          |                                            |
+----------------------------------------------+--------------------------------------------+


+------------------------------------+---------------------------------------------------------+
| RPC_WALLET_ENCRYPTION_FAILED (-16) | *Failed to encrypt the wallet*                          |
+====================================+=========================================================+
| "Failed to sign transaction"       | Transaction was not signed, sign transaction and retry. |
+------------------------------------+---------------------------------------------------------+


+---------------------------------------------------------+---------------------------------------------+
| RPC_WALLET_KEYPOOL_RAN_OUT (-12)                        | *Keypool ran out, call keypoolrefill first* |
+=========================================================+=============================================+
| "Could not generate a taddr to use as a change address" | Call keypoolrefill and retry.               |
+---------------------------------------------------------+---------------------------------------------+


.. |c| replace:: Command
.. |p| replace:: Parameters
.. |d| replace:: Description
