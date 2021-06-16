## Alert: 交期更改通知
判斷ERP每天轉出的Excel訂單資料中，排定交貨日(OEB16)是否有修改。再列出有修改的結果 email 給所有業務，業務可由業務編號自行核對是否有自己負責的訂單。 <br>

#### 附件說明
1.email.txt: 用來修改寄件人與收件人email，輸入方式請看檔案內文。<br>
2.fileName.txt: 用來修改欲比較的兩個檔案名稱，輸入方式請看檔案內文。<br>

#### 資料處理流程說明
假設ERP每天轉出一個月的訂單資料。讀取連續兩天的data後，利用欄位pk判斷資料重複並進行處理，可得出交期有修改的資料。最後將業務需要的資訊整合並轉html，再以email寄出。<br>
1.	pk (不能重複) = 訂單編號 + 項次
2.	df = 昨天的資料 [4/1-5/1] = [4/1 + 4/2-5/1(交期更改前+未改的)]
3.	df1 = 今天的資料 [4/2-5/2] = [5/2 + 4/2-5/1(交期更改後+未改的)]
4.	tmp [4/1 + 5/2 + 4/2-5/1(交期更改前+交期更改後+未改的*2)]<br>
      = 昨天的資料 + 今天的資料 (全部以新row加入)<br>
      = [4/1 + 4/2-5/1(交期更改前+未改的)] + [5/2 + 4/2-5/1(交期更改後+未改的)]<br>
      = df + df1<br>
5.	all_diff [4/1 + 5/2 + 4/2-5/1(交期更改前+交期更改後+未改的)]<br>
      = 昨天跟今天所有不重複的資料 (含交期更改前後pk相同的訂單)<br>
      = 昨天的資料 + 今天的資料 - 兩天中每個column都一樣的資料<br>
      = [4/1 + 5/2 + 4/2-5/1(交期更改前+交期更改後+未改的*2)] – [4/2-5/1未改的]<br>
      = tmp - tmp中每個column都一樣的資料
6.	pk_diff [4/1 + 5/2 + 4/2-5/1(未改的)]<br>
      = 昨天跟今天所有pk不重複的資料 (不含交期被更改過的所有訂單)<br>
      = all_diff - all_diff中pk一樣時的所有資料<br>
      = all_diff刪除所有pk一樣的資料<br>
7.	first [4/1 + 4/2-5/1(交期更改前+未改的)]<br>
	= 昨天跟今天所有pk不重複的資料 (含交期更改前的訂單，不含交期更改後的訂單)<br>
      = all_diff - all_diff中pk一樣時今天的資料<br>
      = all_diff中pk一樣時保留昨天的資料，刪除今天的<br>
8.	last [5/2 + 4/2-5/1(交期更改後+未改的)]<br>
	= 昨天跟今天所有pk不重複的資料 (含交期更改後的訂單，不含交期更改前的訂單)<br>
      = all_diff - all_diff中pk一樣時昨天的資料<br>
      = all_diff中pk一樣時保留今天的資料，刪除昨天的
9.	old [4/2-5/1交期更改前的]<br>
      = 昨天所有被更改過交期的資料<br>
       =[4/1 + 4/2-5/1(交期更改前+未改的)] + [4/1 + 4/2-5/1交期未改的] – [5/2 + 4/2-5/1交期未改的]<br>
       = first + pk_diff – 兩者pk一樣的資料<br>
10.	new [4/2-5/1交期更改後的]<br>
      = 今天所有被更改過交期的資料<br>
       = [5/2 + 4/2-5/1(交期更改後+未改的)] + [5/2 + 4/2-5/1交期未改的] – [5/2 + 4/2-5/1交期未改的]<br>
      = last + pk_diff – 兩者pk一樣的資料<br>
11.	final = 最後要寄給業務的通知內容
