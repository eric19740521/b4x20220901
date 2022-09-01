# b4x20220901
B4X實驗: jRDC2(Remote Database Connector)/WebServer/webAPI深入探討01

B4X實驗: jRDC2(Remote Database Connector)/WebServer/webAPI深入探討01
參考資料:


1.jRDC版本2,目前用戶端使用的函數有底下
就不探討使用.之前影片有講解

	jRDC2Utils.AddCommand(cmdList,"insert_article",Array(1,"a1"))
	jRDC2Utils.AddCommand(cmdList,"insert_article",Array(2,"a2"))
	wait for (jRDC2Utils.ExecuteCommands(cmdList)) complete (Result As Int)

	Dim cmd1 As DBCommand = CreateCommand("DeleteAll_animal", Array())
	Dim j As HttpJob = CreateRequest.ExecuteBatch(Array(cmd1), Null)

	Dim cmd As DBCommand=jRDC2Utils.CreateCommand("select_article",Array())
	Wait For(jRDC2Utils.ExecuteQuery(cmd)) complete(res As DBResult)


		Dim Parametros() As String = Array As String(txtNo.Text.Trim, txtPassword.Text.Trim)
		Wait For(jRDC.GetRecord("Login", Parametros)) Complete (Answer As Map)


			Dim Parametros() As String =  Array As String(Item.Get("mem_no"),Item.Get("mem_name"),Item.Get("mem_password"),Item.Get("mem_memo"))
			Wait For(B4XPages.MainPage.jRDC.InsertUpdateRecord("member_Insert", Parametros )) Complete (Answer As Map)


2.理解jRDC2 Server-client之間的運作 http
1)client送指令(查詢/批次)
j.PostBytes(link & "?method=" & Method , Data)
http://127.0.0.1/rdc?method=query2 跟 http://127.0.0.1/rdc?method=batch2)

查詢query2
Dim data() As Byte = ser.ConvertObjectToBytes(CreateMap("command": Command, "limit": Limit,  "version": VERSION))
批次batch2
	ser.ConvertObjectToBytesAsync(CreateMap("commands": ListOfCommands,  "version": VERSION), "ser")
 

2)server收到指令(查詢/批次)
Dim method As String = req.GetParameter("method")

批次batch2
q = ExecuteBatch2(con, in, resp)
查詢query2
q = ExecuteQuery2(con, in, resp)

3)server回傳 (查詢query2)
Dim res As DBResult
	Dim data() As Byte = ser.ConvertObjectToBytes(res)
	resp.OutputStream.WriteBytes(data, 0, data.Length)

4)client接收
req.HandleJobAsync(j, "req")


2.對jRDC2  客戶端 小部分 改造. 
用意:對指令簡化.易於記憶
ExecuteCommands
ExecuteQuery
SQLExec

下次再做Server...分次講解


xyzRDC 取代 jRDC2




