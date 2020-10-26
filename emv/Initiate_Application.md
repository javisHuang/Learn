Initiate Application  
=============  
SelectCard(應用選擇)  
-------------  
> 初始在做select card時，以感應來說有個對應的入口，而這個入口為往下取得卡片資料的初始點，  
因此在執令上所使用的為  
00A40400 + "2PAY.SYS.DDF01"的長度 + "2PAY.SYS.DDF01"的Hex String + 00  
回傳值的範例  
6F2F840E325041592E5359532E4444463031A51DBF0C1A61184F07A0000000031010500A564953412044454249548701019000  
這個值可使用emvlab.org/tlvutils來解析，如  
https://emvlab.org/tlvutils/?data=6F2F840E325041592E5359532E4444463031A51DBF0C1A61184F07A0000000031010500A564953412044454249548701019000  
解析後會有  
```
6F File Control Information (FCI) Template
  84 Dedicated File (DF) Name 等於PPSE AID
  325041592E5359532E4444463031
  A5 File Control Information (FCI) Proprietary Template
      BF0C File Control Information (FCI) Issuer Discretionary Data
      61 Application Template
        4F Application Identifier (AID) – card
          A0000000031010
        50 Application Label
          V I S A D E B I T
        87 Application Priority Indicator
          01
90 Issuer Public Key Certificate

而卡片的基本資料就是在61 Application Template裡
4F為AID選卡使用
50為標籤
87為優先選擇權
```

再來，開始進行select card  
執令為  
00A40400 + "4F的長度" + 4F + 00  
回傳值的範例  
6F378407A1234567891010A52C5004564953418701029F38069F1A025F2A025F2D046E6F656E9F1101019F120C5649534120436C6173736963  
```
6F File Control Information (FCI) Template
 	84 Dedicated File (DF) Name
 	 	A1234567891010
 	A5 File Control Information (FCI) Proprietary Template
 	 	50 Application Label
 	 	 	V I S A
 	 	87 Application Priority Indicator
 	 	 	02
 	 	9F38 Processing Options Data Object List (PDOL)
 	 	 	9F1A025F2A02
 	 	5F2D Language Preference
 	 	 	n o e n
 	 	9F11 Issuer Code Table Index
 	 	 	01
 	 	9F12 Application Preferred Name
 	 	 	V I S A C l a s s i c
```