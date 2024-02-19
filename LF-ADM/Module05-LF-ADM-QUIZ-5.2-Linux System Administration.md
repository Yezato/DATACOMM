# Quiz 5.2: Install Package from Source
Context: You are asked to install a GNU software package from source code.

First, configure the software package at /pkg/emacs-26.3 with --without-x --with-x-toolkit=no --with-gnutls=no options.

> Note: The software package has been unpacked and all tools required for a configuration have been pre-installed for your convenience.

Next, build the software package at /pkg/emacs-26.3.

> Note: All tools and dependent libraries required for compilation have been pre-installed for your convenience.

Finally, install the built software package.

Verify installed software with command "emacs --version"

- `cd /pkg/emacs-26.3/`
- `ls`
- `./configure --without-x --with-x-toolkit=no --with-gnutls=no`
- `make`
- `make install`
- `emacs --version`
