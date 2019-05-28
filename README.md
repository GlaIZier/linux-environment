# Ansible linux (tested on Ubuntu) environment initializer for developers
[![Build Status](https://travis-ci.org/GlaIZier/linux-environment.svg?branch=master)](https://travis-ci.org/GlaIZier/macos-environment)

## What does it do
1. adds configs (dotfiles like .bashrc) from a github repository to your home directory '~'
2. installs packages
3. installs sdkman and sdk applications ([sdkman role](https://github.com/Comcast/ansible-sdkman))
4. installs zsh, oh-my-zsh and configures it ([oh-my-zsh role](https://github.com/viasite-ansible/ansible-role-zsh))
5. creates a pair of ssh keys
6. installs additional npm, python, ruby and php packages
7. creates folders in the home directory

## Dependencies
[sdkman role](https://github.com/Comcast/ansible-sdkman)\
[oh-my-zsh role](https://github.com/viasite-ansible/ansible-role-zsh) \
[cmprescott.chrome](https://github.com/cmprescott/ansible-role-chrome) \
In order to install configs in the home directory, a repository with them is required. My [repository](https://github.com/GlaIZier/configs) is used by default

## Issues 
1. This role is not fully idempotent. This forcefully re-installs dotfiles, configs and also sdkman packages if their vesions are not specified. 
2. For Russia. If https://get.sdkman.io/ is unreachable, make sure you use proxy by, say, `export {http,https}_proxy="<server>"`. To unset `unset {http,https}_proxy` or reload the machine. 
3. Sometimes even with proxy enabled sdkman does not want to download packages (progress bar is not showing at all and the error about curl or a missing package occurs. No idea why this happens. Maybe switching from offline and online mode could help. Or remove `rm -rf ~/.sdkman` and reinstall sdkman
4. On Ubuntu 18.04.2, you need to install git manually beforehand
## Installation
  1. [Install Ansible](http://docs.ansible.com/intro_installation.html).
  2. Clone this repository to your local drive.
  3. Run `$ ansible-galaxy install -r requirements.yml` inside this directory to install required Ansible roles.
  4. Adjust packages to be installed in default.config.yml or create a custom config.yml and overwrite them according to your necessities
  5. Enable proxy (see issues) if needed

## Run
### Run all tasks
`ansible-playbook main.yml -i inventory -K` inside this directory. Enter your account password when prompted. 
### Run separate tasks
Filter by tags can be used: `ansible-playbook main.yml -i inventory -K --tags "dotfiles,homebrew"` or \
`ansible-playbook main.yml -i inventory -K --tags "dotfiles,homebrew"` \
Or update the config to disable some tasks, e.g. `configure_homebrew: no` \
By using these strategies you can execute tasks one by one.

## Additional setup
1. If you install sdkman, make sure that profiles contain SDKMAN_DIR. Especially if you tried to run the playbook several times
2. Download and install [powerline](https://github.com/powerline/fonts) fonts \ 
   Choose fonts for powerline (e.g. Droid Sans Mono for Powerline or another one): \
   More info in Readme of the [oh-my-zsh role](https://github.com/viasite-ansible/ansible-role-zsh)
3. Install color scheme for the terminal (e.g Solarized dark)
4. Install transparency and blur for the terminal (e.g. 90%)

## Config
You can override any of the defaults configured in `default.config.yml` by creating a `config.yml` file and setting the overrides in that file. For example, you can customize the installed packages and apps with something like:
    
    gem_packages:
      - name: bundler
        state: latest
    
    npm_packages:
      - name: webpack
    
    pip_packages:
      - name: mkdocs

Any variable can be overridden in `config.yml`; see the supporting roles' documentation for a complete list of available variables.

## Idempotence
This playbook has several inidempotent steps:
1. .zshrc can be replaced by the dotfiles task and then by the .oh-my-zsh role if you run the whole playbook repeatedly. This is done to make this playbook work with with an arbitrary number of included tasks. 
2. The sdkman role changes candidates every time (maybe because the version is not specified?)
3. Sdkman role may fail to add SDKMAN_HOME in profiles when you run this playbook several times. If so, add it manually.
4. Update repositories before installing packages
5. Zsh role may make some changes

## More information
More information can be found in readmes for every role
