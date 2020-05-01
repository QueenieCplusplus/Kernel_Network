# Kernel Network

Linux Kernel 實作了 Network Scheduler (網路排程器)、或稱為 Packet Scheduler (封包排程器)的頻寬控制功能。透過它，可以控制各網路裝置傳送資料的吞吐量。使用頻寬控制功能，可以優先服務重要的通訊，調整各服務效能。

詞彙錦囊：

* TBF:

  Token Bucket Filter, 是某種控制封包格式與頻寬限制的演算法，對於突發傳輸 Burst Transmission 和一般的網路傳輸有用。
  
  https://en.wikipedia.org/wiki/Burst_transmission

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

# Bandwidth
   
# Tunneling
   
# Bridge
   
 # VLAN
