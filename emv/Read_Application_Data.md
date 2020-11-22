Read_Application_Data  
=============  
Initiate Application完成後  
可能回傳為  
800E7D00400101004801030150010300  
或者
7716820239009410100101011002020018010200200102009000
這邊主要取得82及94  
而取得的方式，先判斷根據是否有77的Tag  
如果有77就以BERTLV解析，直接取得82及94  
如果沒有77就以切割方式取得
800E7D00400101004801030150010300分解
7D00為82
400101004801030150010300為94  
接著需讀取94的AFL每個區的資料，以4個byte為一組拆解，並針對4個byte在分解讀資料指令，但第4個byte為計算OfflineDataAuth所使用(在此先不多做說明)  
```
40010100
拆解：SFI取第1個byte 40(需轉換hex 28在加4，32在hex to string為2，並左補0為02)第一條記錄取第2個byte 01，最後一條記錄取第3個byte 01
指令：
	00 B2 01 02 00
48010301
拆解：SFI取第1個byte 48(需轉換hex 30在加4，34在hex to string為4，並左補0為04)第一條記錄取第2個byte 01，最後一條記錄取第3個byte 03
指令：
	00 B2 01 04 00
	00 B2 02 04 00
	00 B2 03 04 00
50010300
拆解：SFI取第1個byte 50(需轉換hex 32在加4，36在hex to string為6，並左補0為06)第一條記錄取第2個byte 01，最後一條記錄取第3個byte 03
指令：
	00 B2 01 06 00
	00 B2 02 06 00
	00 B2 03 06 00
```  
  
依序上述指令進行  
00B2010200 回傳  
703e5f200f46554c4c2046554e4354494f4e414c57116228000100001117d101220101234567899f1f16303130323033303430353036303730383039304130429000
```
70 EMV Proprietary Template
 	5F20 Cardholder Name
 	 	F U L L F U N C T I O N A L
 	57 Track 2 Equivalent Data
 	 	622800xxxxxx1117D10122010123456789
 	9F1F Track 1 Discretionary Data
 	 	0 1 0 2 0 3 0 4 0 5 0 6 0 7 0 8 0 9 0 A 0 B
90 Issuer Public Key Certificate
```  
00B2010400 回傳  
700e5a0862280001000011175f3401019000
```
70 EMV Proprietary Template
 	5A Application Primary Account Number (PAN)
 	 	622800xxxxxx1117
 	5F34 Application Primary Account Number (PAN) Sequence Number
 	 	01
90 Issuer Public Key Certificate
```  
00B2020400 回傳  
70558c1d9f34039f02069f03069f1a0295059b025f2a029a039f21039c019f37048d1c8a029f02069f03069f1a0295059b025f2a029a039f21039c019f37049f0e0500000000009f0f0500000000009f0d0500000000009000
```
70 EMV Proprietary Template
 	8C Card Risk Management Data Object List 1 (CDOL1)
 	 	9F34039F02069F03069F1A0295059B025F2A029A039F21039C019F3704
 	8D Card Risk Management Data Object List 2 (CDOL2)
 	 	8A029F02069F03069F1A0295059B025F2A029A039F21039C019F3704
 	9F0E Issuer Action Code – Denial
 	 	0000000000
 	9F0F Issuer Action Code – Online
 	 	0000000000
 	9F0D Issuer Action Code – Default
 	 	0000000000
90 Issuer Public Key Certificate
```  
將所有回傳資料依BERTLV解析取得  
5F20、57、9F1F、5A、5F34、8C、8D、9F0E、9F0F、9F0D等…