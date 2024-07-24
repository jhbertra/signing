# Key Management for Delegators and Voters

## Step 1: Install necessary software

### MacOS instructions:

1. Open the "Terminal" application, e.g. via spotlight
2. Install the `Homebrew` package manager if you have not already done so: https://brew.sh/
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```
3. Install `openssl` using Homebrew
   ```bash
   brew install openssl
   ```

### Linux:

TODO

### Windows:

TODO

## Step 2: Create a Ed25519 Private Key

1. Decide where you want to keep your private keys. You will need to remember
   where they are for future use.
   a. When you open a terminal by default, it will be in your user's default
   "home" directory. On Linux this will look something like `/home/<your-username>`.
   on MacOS, this will look something like `/Users/<your-username>`. On
   Windows, TODO. On MacOS and Linux, `~` is an abbreviation for this
   directory.
   b. One place you can consider keeping them that is easy to remember and easy
   to access from a terminal is a directory called `private-keys` in your home
   directory. If you want to do this, create the directory from the terminal
   using the command

   ```bash
   mkdir ~/private-keys
   ```

2. Create the private key with the command:

   ```bash
   openssl genpkey -aes256 -algorithm ed25519 -out test.private
   ```
