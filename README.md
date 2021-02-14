# Straightedge validator instructions
(for Ubuntu)

Minimum recommended hardware requirements: 1 vCPU; 2gb RAM; 50gb SSD

# Update Ubuntu
`sudo apt update`

`sudo apt upgrade -y`

`apt install make`

`apt install gcc`

`apt install jq`

# Install Go
`sudo rm -rf /usr/local/go`

(this removes any existing old Go installation)

`curl https://dl.google.com/go/go1.15.7.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -`

(this downloads and installs Go)

`cat <<'EOF' >>$HOME/.profile`

`export GOROOT=/usr/local/go`

`export GOPATH=$HOME/go`

`export GO111MODULE=on`

`export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin`

`EOF`

`source $HOME/.profile`

(these commands update environment variables to include Go)

`go version`

(checks to see if Go was installed)

# Install Straightedge
`git clone https://github.com/heystraightedge/straightedge`

`cd straightedge`

`make install`

`strcli version`

(checks to see if Straightedge was installed)

# To do
1. instructions for setting Straightedge configurations & download genesis.json
2. instructions for setting daemon configuration and starting daemon (sync the node)
3. how to create validator (once node is synced)
4. common commands used by validator
5. links to other resources
