# Kernel Network

Linux Kernel 實作了 Network Scheduler (網路排程器)、或稱為 Packet Scheduler (封包排程器)的頻寬控制功能。透過它，可以控制各網路裝置傳送資料的吞吐量。使用頻寬控制功能，可以優先服務重要的通訊，調整各服務效能。

詞彙錦囊：

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
                                                        
  此檔案定義了
  
  （1）優先順序
  
  （2）以 class 為單位分配傳送頻寬
  
  （3）各 class 分別準備設定檔
  
  （4）對設定檔描述頻寬的控制方法
  
  * Device:
  
    設定了要控制頻寬的網路裝置，格式如下：
    
    DEVICE=<ifname>,<bandwidth>[,<weight>]
  
    ifname 是網路介面的名稱。
    
    bandwidth 是網路裝置的實體頻寬。
    
    weight 是 root class 的權重，權重越大， root class 一次處理的資料比例越大，建議是 bandwidth 的 1/10。

# Bandwidth
   
# Tunneling
   
# Bridge
   
 # VLAN
