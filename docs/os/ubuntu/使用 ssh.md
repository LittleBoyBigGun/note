

ssh 支持密码和非对称密钥两种认证方式， 后者完成认证后无需输入密码， 方便且安全。本文着重介绍密钥认证方式的大致原理， 不对具体认证过程做介绍， 对此感兴趣者请自行从网络获取。

为了叙述的方便， 假定 ssh 连接的发起者为 client, ssh 连接的受理者为 server。连接发起前确保满足以下前置条件

- client 端已正确安装 ssh 客户端, ssh-keygen(可选), ssh-copy-id （可选）
- server 端已正确安装 ssh 服务端（sshd), 且正确配置

client 使用 `ssh-keygen` 生成密钥对， 把私钥复制到  .ssh  目录

- `$HOME/.ssh(linux)` 
- `c:/Users/<your user name>/.ssh(windows)`  

公钥复制到 server 端 .ssh 目录的 authorized_keys 文件， 可以通过 ssh-copy-id 工具来实现， 也可以手动编辑 authorized_keys 文件。

连接发起后， 会在 client 的 .ssh 目录 known-hosts 文件中记录 server 的信息。

自此 ssh 密钥认证方式就完成。本质上， client 和 server 端交换操作也没问题， 同样可以完成认证。

# authorized_keys

记录 ssh 链接发起者公钥， 可以使用 `ssh-copy-id` 工具完成记录， 同样可以手动编辑

# known_hosts

记录 ssh 链接受理者信息， 自动记录， 无需人为干涉.

# ssh-keygen

生成密钥对 `ssh-keygen -t rsa -b 4096`

# ssh-copy-id

发送公钥 `ssh-copy-id -i /local/path/to/pubkey user@host(对端)` 

