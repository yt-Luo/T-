### Alert: 交期更改通知
判斷ERP每天轉出的Excel訂單資料中，排定交貨日(OEB16)是否有修改。再列出有修改的
結果 Email 給所有業務，業務可由業務編號自行核對是否有自己負責的
訂單。 <br>
### 資料處理說明
1.	pk (不能重複) = 訂單編號 + 項次
2.	df = 昨天的資料 [4/1-5/1] = [4/1 + 4/2-5/1(交期更改前+未改的)]
3.	df1 = 今天的資料 [4/2-5/2] = [5/2 + 4/2-5/1(交期更改後+未改的)]
4.	tmp [4/1 + 5/2 + 4/2-5/1(交期更改前+交期更改後+未改的*2)]<br>
      = 昨天的資料 + 今天的資料 (全部以新row加入)<br>
      = [4/1 + 4/2-5/1(交期更改前+未改的)] + [5/2 + 4/2-5/1(交期更改後+未改的)]<br>
      = df + df1<br>
5.	df2 [4/1 + 5/2 + 4/2-5/1(交期更改前+交期更改後+未改的)]<br>
      = 昨天跟今天所有不重複的資料 (交期更改前後pk相同)<br>
      = 昨天的資料 + 今天的資料 - 兩天中每個column都一樣的資料<br>
      = [4/1 + 5/2 + 4/2-5/1(交期更改前+交期更改後+未改的*2)] – [4/2-5/1未改的]<br>
      = tmp - tmp中每個column都一樣的資料
6.	dupl [4/1 + 5/2 + 4/2-5/1(未改的)]<br>
      = df2 - df2中pk一樣時的所有資料<br>
      = df2刪除所有pk一樣的資料<br>
7.	first [4/1 + 4/2-5/1(交期更改前+未改的)]<br>
      = df2 - df2中pk一樣時今天的資料<br>
      = df2中pk一樣時保留昨天的資料，刪除今天的<br>
8.	last [5/2 + 4/2-5/1(交期更改後+未改的)]<br>
      = df2 - df2中pk一樣時昨天的資料<br>
      = df2中pk一樣時保留今天的資料，刪除昨天的
9.	old [4/2-5/1交期更改前的]<br>
      = [4/1 + 4/2-5/1(交期更改前+未改的)] + [4/1 + 4/2-5/1交期未改的] – [5/2 + 4/2-5/1交期未改的]<br>
      = first + dupl – 兩者pk一樣的資料<br>
10.	new [4/2-5/1交期更改後的]<br>
      = [5/2 + 4/2-5/1(交期更改後+未改的)] + [5/2 + 4/2-5/1交期未改的] – [5/2 + 4/2-5/1交期未改的]<br>
      = last + dupl – 兩者pk一樣的資料<br>
