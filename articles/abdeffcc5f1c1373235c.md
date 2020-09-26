---
title: "ReadyNAS の LAG 使用時の IP を DHCP で設定するときのメモ"
emoji: "♻️"
type: "tech"
topics: ["rtx1210", "ReadyNas", "DHCP"]
published: true
---
- ReadyNAS214: 6.10.1
- RTX1210: Rev.14.01.34

ReadyNASのDHCPクライアントはRFC2131完全対応してないらしく、クライアント識別に chaddr フィールドを使用しているようである。

なのでRTX1210のDHCPに `dhcp server rfc2131 compliant except use-clientid` を設定すれば行ける。

http://www.rtpro.yamaha.co.jp/RT/manual/rt-common/dhcp/dhcp_server_rfc2131_compliant.html
