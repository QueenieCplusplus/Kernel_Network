# Kernel Network

Linux Kernel 實作了 Network Scheduler (網路排程器)、或稱為 Packet Scheduler (封包排程器)的頻寬控制功能。透過它，可以控制各網路裝置傳送資料的吞吐量。使用頻寬控制功能，可以優先服務重要的通訊，調整各服務效能。

# Pre-Study

https://github.com/QueenieCplusplus/SSCP_Network/blob/master/README.md


# Bandwidth, 頻寬設定

詞彙錦囊：

* TBF:

  Token Bucket Filter, 是某種控制封包格式與頻寬限制的演算法，對於突發傳輸 Burst Transmission 和一般的網路傳輸有用。對於網路設定而言，TBF 是 queueing discipline 序列的規範之一，負責暫存封包。 對於普遍常見的 NIC 來說，其頻寬速度為 Gigabit Ethernet (位元)，Buffer 的大小推薦設為 10kb/8。
  
  https://en.wikipedia.org/wiki/Burst_transmission
  
  https://en.wikipedia.org/wiki/Token_bucket

* CBQ:

  Class Based Queue, 類別基礎的序列。需要先安裝 iproute 套件。 
  
     //
     
     
            # cat /etc/sysconfig.cbq/cbq-0000.example
            DEVICE=eth0,10Mbit,1Mbit
            Rate=128Kbit
            WEIGHT=10Kbit
            PRIO=5
            RULE=192.168.1.0/24
            
                                                        //
                                                        
  * 此檔案定義了
  
  （1）優先順序
  
  （2）以 class 為單位分配傳送頻寬
  
  （3）各 class 分別準備設定檔
  
  （4）對設定檔描述頻寬的控制方法
  
     * DEVICE:
  
    設定了要控制頻寬的網路裝置，格式如下：
    
        DEVICE=<ifname>,<bandwidth>[,<weight>]
  
    * ifname 是網路介面的名稱。
    
    * BANDWIDTH 是網路裝置的實體頻寬。
    
    * WEIGHT 是 root class 的權重，權重越大， root class 一次處理的資料比例越大，建議是 bandwidth 的 1/10。
    
    * RATE 是分配給本 class 的頻寬，單位是 bit || bps，bit == bits/sec，bps == bytes/sec。
    
    * WEIGHT 是本 class 的權重，建議為 Rate 的 1/10，權重值越大，本 class 在 root class 之內一次處理的資料量比例越高。
    
    * PRIO 是本 class 的優先度，可指定範圍為 1 ~ 8，數值越小，優先度越高。
    
    * RULE 是指定控制頻寬的對象。可以限定通訊對象的 IP 位置或是 port 編號，所以可以只控制 HTTP 或是 FTP 的頻寬。格式如下：
    
          RULE = [[saddr[/prefix][:port[/mask]],][daddr[/prefix]][:port[/mask]]]
      
    * TIME 時間參數可以根據時間帶與星期幾，變更頻寬速度與權重設定。
    
    
    
          TIME=[<dow>,<dow>,...,<dow>/]<form>-<till>;<rate>/<weight>[/<peak>]
          
 啟動腳本：
 
 CBQ 設定是透過 tc, traffic control, 不過 tc 指令的選項很多，非常複雜。
 
     # chmod 777 /usr/share/doc/iproute-2.6.18/examples/cbq.init-v0.7.3
   
# Tunneling, 隧道

    無論是 OSI 第二層的 TAP 裝置抑或是第三層 TUN , Linux Ｋernel 引進 TUN/TAP 驅動程式，輕易建立了 TUN/TAP 裝置，實現隧道網路。
    

* Layer 3


      如下針對 TUN device tun0 執行 ifconfig。

      #ifconfig tun0 

      tun0 Link encap: (略)
      inet addr: 192.168.1.1 P-t-P:192.168.1.2 Mask:255.255.255.255(or 248)
      UP POINTOPOINT RUNNING NOARP MULTICAST MTU:1500 (1字節==8bits=1byte) Metric:1
      RX packets: 0 errors: 0 dropped: 0 overruns: 0 frames: 0
      TX packets: 0 errors: 0 dropped: 0 overruns: 0 carriers: 0
      collisions: 0 txqueuelen: 500
   
   
* Layer 2


      如下針對 TAP device tun0 執行 ifconfig。

      #ifconfig tap0 

      tap0 Link encap: Ethernet HWaddr D2:76:E4:DD:E0:F8 
      Broadcast MULTICAST MTU:1500 (1字節==8bits=1byte) Metric:1
      RX packets: 0 errors: 0 dropped: 0 overruns: 0 frames: 0
      TX packets: 0 errors: 0 dropped: 0 overruns: 0 carriers: 0
      collisions: 0 txqueuelen: 500


                            ethernet frame sender                 thernet frame rcv
         OS -------> TAP Device -------------> user-space app ----> network stack -----> TAP Device -------> OS
         
         ///////

# Bridge

OS 啟動後，可設定自動建立 Bridge，設定方式隨著使用的 distribution 而異。

RED Hat Linux:

     /etc/sysconfig/network-scripts 目錄下的 ifcfg-br0 檔名
     
        DEVICE=br0
        TYPE=Bridge
        BOOTPROTO=dhcp
        ONBOOT=yes
        STP=off
        
     另外，要為連上 bridge 的網路介面 編輯 ifcfg-<interface-name> 設定檔，
     將之設定為 Bridge，同時加上 NM_CONTROLLED = no 讓他們不被 Network Mgmt 管理
     
    ifcfg-eth0 檔名
     
        DEVICE=etho
        HWADDR= **:**:**:**:**:**
        NM_CONTROLLED=n0
        ONBOOT=yes
        BRIDGE=br0   
   
 # VLAN
 
 使用 vconfig 指令
 
Linux 實做了 802.1Q tagged VLAN 功能，Vlan 就是將乙太網路進行虛擬切割的功能，實體的乙太網路可以透過 VLAN ID 切成許多的虛擬網路。 VLAN 環境下，具備相同的 VLAN ID 的機器才能彼此通訊，即便
     
