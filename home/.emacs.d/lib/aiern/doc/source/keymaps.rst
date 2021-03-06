.. _chapter-keymaps:

Keymaps
=======

aiern's key bindings are stored in a number of different keymaps.  Each
state has a *global keymap*, where the default bindings for that state
are stored.  They are named ``aiern-normal-state-map``,
``aiern-insert-state-map``, and so on.  The bindings in these maps are
visible in all buffers currently in the corresponding state.

These keymaps function like ordinary Emacs keymaps and may be modified
using the Emacs function ``define-key``:

.. code-block:: elisp

   (define-key aiern-normal-state-map (kbd "w") 'some-function)

This binds the key :kbd:`w` to the command ``some-function`` in normal
state.  The use of ``kbd`` is optional for simple key sequences, like
this one, but recommended in general.

Most of aiern's bindings are defined in the file ``aiern-maps.el``.

To facilitate shared keybindings between states, some states may
activate keybindings from other states as well.  For example, motion
state bindings are visible in normal and visual state, and normal
state bindings are also visible in visual state.

Each state also has a *buffer-local keymap* which is specific to the
current buffer, and which takes precedence over the global keymap.
These maps are most suitably modified by a mode hook.  They are named
``aiern-normal-state-local-map``, ``aiern-insert-state-local-map``, and
so on.

.. code-block:: elisp

   (add-hook 'some-mode-hook
             (lambda ()
               (define-key aiern-normal-state-local-map
                           (kbd "w") 'some-function)))

For convenience, the functions :elisp:ref:`aiern-global-set-key` and
:elisp:ref:`aiern-local-set-key` are available for setting global and
local state keys.

.. elisp:autofunction:: aiern-global-set-key

.. elisp:autofunction:: aiern-local-set-key

The above examples could therefore have been written as follows:

.. code-block:: elisp

   (aiern-global-set-key 'normal (kbd "w") 'some-function)

   (add-hook 'some-mode-hook
             (lambda ()
               (aiern-local-set-key 'normal (kbd "w") 'some-function)))


aiern-define-key
---------------

aiern provides the macro :elisp:ref:`aiern-define-key` for adding state
bindings to ordinary keymaps.  It is quite powerful, and is the
preferred method for fine-tuning bindings to activate in specific
circumstances.

.. elisp:autofunction:: aiern-define-key

There follows a brief overview of the main functions of this macro.

- Define a binding in a given state

  .. code-block:: elisp

     (aiern-define-key 'state 'global (kbd "key") 'target)

- Define a binding in a given state in the current buffer

  .. code-block:: elisp

     (aiern-define-key 'state 'local (kbd "key") 'target)

- Define a binding in a given state under the *foo-mode* major mode.

  .. code-block:: elisp

     (aiern-define-key 'state foo-mode-map (kbd "key") 'target)

  Note that ``foo-mode-map`` is unquoted, and that this form is safe
  before ``foo-mode-map`` is loaded.

- Define a binding in a given state under the *bar-mode* minor mode.

  .. code-block:: elisp

     (aiern-define-key 'state 'bar-mode (kbd "key") 'target)

  Note that ``bar-mode`` is quoted, and that this form is safe before
  ``bar-mode`` is loaded.


The macro :elisp:ref:`aiern-define-key` can be used to augment existing
modes with state bindings, as well as creating packages with custom
bindings.  For example, the following will create a minor mode
``foo-mode`` with normal state bindings for the keys :kbd:`w` and
:kbd:`e`:

.. code-block:: elisp

   (define-minor-mode foo-mode
     "Foo mode."
     :keymap (make-sparse-keymap))

   (aiern-define-key 'normal 'foo-mode "w" 'bar)
   (aiern-define-key 'normal 'foo-mode "e" 'baz)

This minor mode can then be enabled in any buffers where the custom
bindings are desired:

.. code-block:: elisp

   (add-hook 'text-mode-hook 'foo-mode)  ; enable alongside text-mode


Leader keys
-----------

aiern supports a simple implementation of Vim's *leader* keys.  To bind
a function to a leader key you can use the expression ``<leader>`` in
a key mapping, e.g.

.. code-block:: elisp

   (aiern-define-key 'normal 'global (kbd "<leader>fs") 'save-buffer)

Likewise, you can use the expression ``<localleader>`` to mimic Vim's
local leader, which is designed for mode-specific key bindings.

You can use the function :elisp:ref:`aiern-set-leader` to designate
which key acts as the leader and the local leader.

.. elisp:autofunction:: aiern-set-leader
