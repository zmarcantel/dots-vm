# -*- mode: ruby -*-
# vi: set ft=ruby :
#

#===============================================================================
# VM settings
#===============================================================================

BOX_CPUS=1
BOX_RAM_MB=4096
BASE_BOX="ubuntu/xenial64"
BOX_NAME=File.basename(Dir.getwd)
BOX_CIDR_24="10.0.100"


#===============================================================================
# define install steps for various packages
#===============================================================================

$apt_base = <<-SCRIPT
    apt-get update
    apt-get upgrade -y
SCRIPT
$apt_packages = [
    "git",
    "ssh",
    "make",
    "stow",
    "wget", "curl",
    "software-properties-common"
]
def apt_install(vararg)
    return "apt-get install -y " + vararg
end


# vim

$install_neovim = <<-SCRIPT
    add-apt-repository ppa:neovim-ppa/stable
    apt-get update
    apt-get install -y neovim python-dev python-pip python3-dev python3-pip
SCRIPT



# compilers / languages

RUST_TOOLCHAIN="nightly"
$install_rust = "curl https://sh.rustup.rs -sSf | sh -s -- -y --default-toolchain #{RUST_TOOLCHAIN}"

$install_gcc = apt_install("gcc")
$install_clang = apt_install("clang")
$install_cpp = apt_install("gcc clang")


# the export here is only valid until the end of provisioning
GO_VERSION="1.10.1"
$install_golang = <<-SCRIPT
    cd /tmp
    ls go#{GO_VERSION}.linux-amd64.tar.gz || wget https://dl.google.com/go/go#{GO_VERSION}.linux-amd64.tar.gz
    tar -C /usr/local -xzf go#{GO_VERSION}.linux-amd64.tar.gz
SCRIPT


# dot files


# cmake needed for ycmd
$install_dots = <<-SCRIPT
    sudo apt-get install -y cmake zsh
    sudo chsh -s /usr/bin/zsh vagrant
    ls ~/dots || git clone https://github.com/zmarcantel/dots ~/dots
    cd ~/dots
    stow vim zsh env
    export GOROOT=/usr/local/go
    export PATH=$PATH:$GOROOT/bin
SCRIPT
$install_dots += "    ~/.vim_install.sh"
["clang", "rust", "go"].each { |c| $install_dots += " --#{c}-completer" }


#===============================================================================
# specify __what__ is installed
#===============================================================================

$install_steps = {
    'base' => {:cmd => $apt_base, :priv => true},
    'apt_pkgs' => {:cmd => apt_install($apt_packages.join(" ")), :priv => true},

    'vim' => {:cmd => $install_neovim, :priv => true},

    'rust' => {:cmd => $install_rust, :priv => false},
    'cpp' => {:cmd => $install_cpp, :priv => true},
    'go' => {:cmd => $install_golang, :priv => true},
        'gopath' => {:cmd => "mkdir -p ~/go", :priv => false},

    'dots' => {:cmd => $install_dots, :priv => false},
}


#===============================================================================
# run baby run
#===============================================================================

Vagrant.configure(2) do |config|
    config.vm.define BOX_NAME, autostart: true, primary: false do |node|
        node.vm.box = BASE_BOX
        node.vm.network :private_network, ip: "#{BOX_CIDR_24}.10"

        node.vm.provider "virtualbox" do |v|
            v.memory = BOX_RAM_MB
            v.cpus = BOX_CPUS
        end

        node.vm.synced_folder ".", "/#{BOX_NAME}"

        $install_steps.each { |name, desc|
            if ARGV[0] == "up" or ARGV[0] == "provision"
                puts "%s  ==>\n    %s" % [name, desc[:cmd].strip]
            end
            config.vm.provision "shell", inline: desc[:cmd], privileged: desc[:priv]
        }

    end # end node
end # end entry
