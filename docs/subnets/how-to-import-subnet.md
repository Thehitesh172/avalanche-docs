# How to Import a Subnet into Avalanche-CLI

## Context

The most probable reason why someone would want to do this,
is if they already deployed a Subnet with Subnet-CLI either to Fuji or Mainnet,
and now would like to use Avalanche-CLI to manage the Subnet.

Refer to the [installation guide](./install-avalanche-cli.md) for instruction about installing
Avalanche-CLI.

Because Subnet-CLI is now deprecated, you can use this tutorial to migrate to Avalanche-CLI.


Similarly, you might have created a Subnet "manually" by issuing transactions
to node APIs, either a local node or public API nodes, with no help of any tool so far,
but would now target Avalanche-CLI integration.

For this How-To, you import the WAGMI Subnet from Fuji.


## Requirements

For the import to work properly, you need:

* The Subnet's genesis file, stored on disk

* The Subnet's SubnetID


## Import the Subnet

For these use cases, Avalanche-CLI now supports the `import public` command.

Start the import by issuing

```shell
avalanche subnet import public
```

The tool prompts for the network from which to import.
The invariant assumption here is that the network is a public network,
either the Fuji testnet or Mainnet.
In other words, importing from a local network isn't supported.

```shell
Use the arrow keys to navigate: ↓ ↑ → ←
? Choose a network to import from:
  ▸ Fuji
    Mainnet
```

As stated earlier, this is from Fuji, so select it.
As a next step, Avalanche-CLI asks for the path of the genesis file on disk:

```shell
✗ Provide the path to the genesis file: /tmp/subnet_evm.genesis.json
```

The wizard checks if the file at the provided path exists,
refer to the checkmark at the beginning of the line:

```shell
✔ Provide the path to the genesis file: /tmp/subnetevm_genesis.json
```

Subsequently, the wizard asks if nodes have already been deployed for this Subnet.

```shell
Use the arrow keys to navigate: ↓ ↑ → ←
? Have nodes already been deployed to this subnet?:
    Yes
  ▸ No
```

### Nodes are Already Validating This Subnet

If nodes already have been deployed, the wizard attempts to query such a node
for detailed data like the VM version. This allows the tool to skip
querying GitHub (or wherever the VM's repository is hosted)
for the VM's version, but rather we'll get the exact version which is actually running on the node.

For this to work, a node API URL is requested from the user, which is used for the query.
This requires that the node's API IP and port are accessible from the machine running
Avalanche-CLI, or the node is obviously not reachable,
and thus the query times out and fails, and the tool exits.
The node should also be validating the given Subnet for the import to be meaningful,
otherwise, the import fails with missing information.

If the query succeeded, the wizard jumps to prompt for the Subnet ID.

```shell
Please provide an API URL of such a node so we can query its VM version (e.g. http://111.22.33.44:5555): http://154.42.240.119:9650
What is the ID of the subnet?: 28nrH5T2BMvNrWecFcV3mfccjs6axM1TVyqe79MCv2Mhs8kxiY
```

The rest of the wizard is identical to the next section,
except that there is no prompt for the VM version anymore.

### Nodes Aren't Yet Validating this Subnet, the Nodes API URL are Unknown, or Inaccessible (Firewalls)

If you don't have a node's API URL at hand, or it's not reachable
from the machine running Avalanche-CLI, or maybe no nodes have even been deployed yet because
only the `CreateSubnet` transaction has been issued, for example, you can query the public APIs.

You can't know for sure what Subnet VM versions the validators are running though,
therefore the tool has to prompt later.
So, select `No` when the tool asks for deployed nodes:

Thus, at this point the wizard requests the Subnet's ID, without which it can't know
what to import. Remember the ID is different on different networks.

From the [Testnet Subnet Explorer](https://subnets-test.avax.network/WAGMI)
you can see that WAGMI's Subnet ID is `28nrH5T2BMvNrWecFcV3mfccjs6axM1TVyqe79MCv2Mhs8kxiY`:

```shell
✔ What is the ID of the subnet?: 28nrH5T2BMvNrWecFcV3mfccjs6axM1TVyqe79MCv2Mhs8kxiY
```

Notice the checkmark at line start, it signals that there is ID format validation.

If you hit `enter` now, the tool queries the public APIs for the given network, and if successful,
it prints some information about the Subnet, and proceeds to ask about the Subnet's type:

```shell
Getting information from the Fuji network...
Retrieved information. BlockchainID: 2ebCneCbwthjQ1rYT41nhd7M76Hc6YmosMAQrTFhBq8qeqh6tt, Name: WAGMI, VMID: srEXiWaHuhNyGwPUi444Tu47ZEDwxTWrbQiuD7FmgSAQ6X7Dy
Use the arrow keys to navigate: ↓ ↑ → ←
? What's this VM's type?:
  ▸ Subnet-EVM
    Custom
```

Avalanche-CLI needs to know the VM type, to hit its repository and select
what VM versions are available.
This works automatically for Ava Labs VMs (like Subnet-EVM).

Custom VMs aren't supported yet at this point, but are next on the agenda.

As the import is for WAGMI, and you know that it's a Subnet-EVM type, select that.

The tool then queries the (GitHub) repository for available releases,
and prompts the user to pick the version she wants to use:

```shell
✔ Subnet-EVM
Use the arrow keys to navigate: ↓ ↑ → ←
? Pick the version for this VM:
  ▸ v0.4.5
    v0.4.5-rc.1
    v0.4.4
    v0.4.4-rc.0
↓   v0.4.3
```

There is only so much the tool can help here, the Subnet manager/administrator
should know what they want to use Avalanche-CLI for, how,
and why they're importing the Subnet.

It's crucial to understand that the correct versions are only known to the user.
The latest might be usually fine, but the tool can't make assumptions about it easily.
This is why it's indispensable that the wizard prompts the user, and the tool requires her to choose.

If you selected to query an actual Subnet validator, not the public APIs, in the preceding step.
In such a scenario, the tool skips this picking.

```shell
✔ v0.4.5
Subnet WAGMI imported successfully
```

The choice finalizes the wizard, which hopefully signals that the import succeeded.
If something went wrong, the error messages provide cause information.
This means you can now use Avalanche-CLI to handle the imported Subnet in the accustomed way.
For example, you could deploy the WAGMI Subnet locally.


For a complete description of options, flags, and the command,
visit the [command reference](./reference-cli-commands.md#subnet-import).

