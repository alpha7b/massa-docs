.. index:: smart contracts, assemblyscript

.. _sc-getting-started:

Getting started
===============

In this section you will learn how to compile your first Massa smart contract.

Setting up a new project
------------------------

Make sure you have a recent version of `Node.js <https://nodejs.org/>`_  and `npm <https://www.npmjs.com/>`_.
Update or `install <https://docs.npmjs.com/downloading-and-installing-node-js-and-npm>`_ them if needed.

`massa-sc-toolkit <https://github.com/massalabs/massa-sc-toolkit/>`_ is a tool that creates a boilerplate
smart-contract project. To create a smart-contract project, invoke the toolkit by running:

.. code-block:: shell

  npx @massalabs/sc-toolkit init my-sc && cd my-sc

You have now npm project created with AssemblyScript installed among other dependencies.
It will be used to generate bytecode from AssemblyScript code.

.. note::
   Massa smart-contract module (`@massalabs/massa-as-sdk`) contains the API you need
   to use to interact with the external world of the smart contract (the node, the ledger...).

Congratulations! Now you have a fully set up project and you are ready to add some code.

.. note::
   A few words on project folders:

   * `assembly` is where the code goes;
   * `build` will be created during compilation and will contain compiled smart contracts.

Create your first smart contract
--------------------------------

Since the beginning of mankind, humans explain how to use a program, a new language,
a service by implementing a *Hello world!*. Your first smart contract will be no exception!

Open the `main.ts` file in the `assembly` directory at the root of your project.
Replace the code in the file by the following code:

.. code-block:: typescript

   import { generateEvent } from "@massalabs/massa-as-sdk";

   export function main(_args: StaticArray<u8>): void {
      generateEvent("Hello world!");
   }

Don't forget to save the file. Before starting compilation, just a few words to describe what is used here:

* line 1: `generateEvent` function is imported from Massa SDK (@massalabs/massa-as-sdk).
  This function will generate an event with the string given as argument. Events can be later recovered using a Massa client.
* line 3: `main` function is exported. This means that the main function will be
  callable from the outside of the WebAssembly module (more about that later).
* line 4: `generateEvent` function is called with "Hello world!". Brian, we are thinking of you!

Now that everything is in place, we can start the compilation step by running the following command:

.. code-block:: shell

   npm run build

Congratulations! You have generated your first smart contract: the `main.wasm` file in `build` directory.
Note that a `deployer.wasm` file has also been generated. It will be used to deploy your contract on Massa network.

.. note::

   If due to bad luck you have an error at compilation time:

   * check that you properly followed all the steps,
   * do a couple a internet research,
   * look for any similar issue (open or closed) in `massa-as-sdk <https://github.com/massalabs/massa-as-sdk/>`_.

   If you find nothing, feel free to contact us on `Discord <https://discord.gg/massa>`_ or
   directly open an `issue <https://github.com/massalabs/massa-as-sdk/issues>`_.

Deploy your smart contract
--------------------------

Your smart contract is now ready to be pushed and executed on the Massa network.
In order to deploy it, you need to own a Massa wallet and some MAS coins on it.

.. note::
   * If you don't have any wallet configured yet, :ref:`create a new one <wallet>`.
   * If you're using a brand new wallet, add some coins by sending your address to
     `testnet-faucet discord channel <https://discord.com/channels/828270821042159636/866190913030193172>`_.

In any case, keep the `Address` and `Secret key` of your wallet, you will use it later.

There are two ways you can deploy your smart contract. The easiest and the recommended way is
to deploy the smart contract with the smart-contract toolkit (:ref:`Option 1 <sc-option-1>` below).

The second option is to deploy the smart contract, through Massa client,
by running your own node (:ref:`Option 2 <sc-option-2>`).

.. _sc-option-1:

Option 1: Deploy your smart contract from the toolkit
-----------------------------------------------------

To send the transaction on the network, you need to provide your wallet's secret key.
This is done using environment variable in `.env` file.

.. code-block::

    cp .env.example .env

This command will create a `.env` file. Now fill it with your wallet secret key.

Then run the following command:

.. code-block:: shell

   npm run deploy

Wait for a few seconds... It should return you the deployed smart contract address.

.. _sc-option-2:

Option 2: Execute your smart contract on your own node
------------------------------------------------------

To execute the smart contract you will need:

- A client configured with an address having coins.
- A smart contract compiled in WebAssembly (see previous step).

Let's go!

Configure the client
~~~~~~~~~~~~~~~~~~~~

Make sure that you have the latest version of the Massa node. If not,
:ref:`install it <testnet-install>` and :ref:`run it <testnet-running>`.

.. note::

   You can also execute your smart-contract on a local sandbox node.
   To learn more about sandbox node, follow this tutorial:
   :ref:`Local network generation <local-network-generation>`.

Execute the smart contract on the node
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Everything is in place, we can now execute the `hello world` smart contract on your
local node with the following command inside the **client cli**:

.. code-block:: shell

   send_smart_contract <address> <path to wasm file> 100000 0 0

.. note::

   We are executing the send_smart_contract command with 6 parameters:

   - <address>: the address of your wallet kept during previous step;
   - <path to wasm file>: the full path (from the root directory to the file extension .wasm)
     of the hello smart contract, generated in the previous chapter.
   - 100000: the maximum amount of gas that the execution of your smart contract is allowed to use.
   - Two 0 parameters that can be safely ignored by now. If you want more info on them, use the command `help send_smart_contract`.

.. note::

   To go inside the **client cli**, open a terminal in `massa/massa-client` directory and run `cargo run`.

If everything went fine, the following message should be prompted:

.. code-block:: shell

   Sent operation IDs:
   <id with numbers and letters>

In that case, you should be able to retrieve the event with the `Hello world` emitted.
Use the following command inside the **client cli**:

.. code-block:: shell

   get_filtered_sc_output_event operation_id=<id with numbers and letters>

If everything went well you should see a message similar to this one:

.. code-block:: shell

    Context: Slot: (period: 627, thread: 22) at index: 0
    On chain execution
    Block id: VaY6zeec2am5i1eKKPzuyvhbzxVU8mts7ykSDj5usHyobJee8
    Origin operation id: wHGoVbp8QSwWxEMzM5nK9CpKL3SpNmxzUF3E4pHgn8fVkJmR5
    Call stack: A12Lkz8mEZ4uXPrzW9WDo5HKWRoYgeYjiQZMrwbjE6cPeRxuSfAG

    Data: Hello world!

Congratulations! You have just executed your first smart contract!
