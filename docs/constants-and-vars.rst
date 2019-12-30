.. index:: type

Environment Variables and Constants
***********************************

.. _types-env-vars:

Environment Variables
=====================

Environment variables always exist in the namespace and are primarily used to provide information about the blockchain or current transaction.

Block and Transaction Properties
--------------------------------

==================== ================ =============================================
Name                 Type             Value
==================== ================ =============================================
``block.coinbase``   ``address``      Current block miner’s address
``block.difficulty`` ``uint256``      Current block difficulty
``block.number``     ``uint256``      Current block number
``block.prevhash``   ``bytes32``      Equivalent to ``blockhash(block.number - 1)``
``block.timestamp``  ``uint256``      Current block epoch timestamp
``msg.gas``          ``uint256``      Remaining gas
``msg.sender``       ``address``      Sender of the message (current call)
``msg.value``        ``uint256(wei)`` Number of wei sent with the message
``tx.origin``        ``address``      Sender of the transaction (full call chain)
==================== ================ =============================================

.. note::

    ``msg.sender`` and ``msg.value`` can only be accessed from public functions. If you require these values within a private function they must be passed as parameters.

.. _constants-self:

The self Variable
-----------------

``self`` is an environment variable used to reference a contract from within itself. Along with the normal :ref:`address<address>` members, ``self`` allows you to read and write to state variables and to call private functions within the contract.

==================== ================ ==========================
Name                 Type             Value
==================== ================ ==========================
``self``             ``address``      Current contract's address
``self.balance``     ``uint256(wei)`` Current contract's balance
==================== ================ ==========================

Accessing State Variables
~~~~~~~~~~~~~~~~~~~~~~~~~

``self`` is used to access a contract's :ref:`state variables<structure-state-variables>`, as shown in the following example:

.. code-block:: python

    state_var: uint256

    @public
    def set_var(value: uint256) -> bool:
        self.state_var = value
        return True

    @public
    @constant
    def get_var() -> uint256:
        return self.state_var


Calling Private Functions
~~~~~~~~~~~~~~~~~~~~~~~~~

``self`` is also used to call :ref:`private functions<structure-functions-private>` within a contract:

.. code-block:: python

    @private
    def _times_two(amount: uint256) -> uint256:
        return amount * 2

    @public
    def calculate(amount: uint256) -> uint256:
        return self._times_two(amount)

.. _types-constants:

Built In Constants
==================

Vyper has a few convenience constants builtin.

================= ================ ==============================================
Name              Type             Value
================= ================ ==============================================
``ZERO_ADDRESS``  ``address``      ``0x0000000000000000000000000000000000000000``
``EMPTY_BYTES32`` ``bytes32``      ``0x0000000000000000000000000000000000000000000000000000000000000000``
``MAX_INT128``    ``int128``       ``2**127 - 1``
``MIN_INT128``    ``int128``       ``-2**127``
``MAX_DECIMAL``   ``decimal``      ``(2**127 - 1)``
``MIN_DECIMAL``   ``decimal``      ``(-2**127)``
``MAX_UINT256``   ``uint256``      ``2**256 - 1``
``ZERO_WEI``      ``uint256(wei)`` ``0``
================= ================ ==============================================

Custom Constants
================

Custom constants can be defined at a global level in Vyper. To define a constant make use of the ``constant`` keyword.

**Example:**

.. code-block:: python

    TOTAL_SUPPLY: constant(uint256) = 10000000
    total_supply: public(uint256)

    @public
    def __init__():
        self.total_supply = TOTAL_SUPPLY

**Advanced Example:**

.. code-block:: python

    units: {
        share: "Share unit"
    }

    MAX_SHARES: constant(uint256(share)) = 1000
    SHARE_PRICE: constant(uint256(wei/share)) = 5

    @public
    def market_cap() -> uint256(wei):
        return MAX_SHARES * SHARE_PRICE
