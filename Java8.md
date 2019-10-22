# Java8 使用

Java 8導入了一個新型態的語法－Lambda。

這個Lambda語法並不是新的語法，在Script Languages和Functional Languages中都可以常常見到

### 什麼是Lambda？

Lambda與一般函數不同的是，Lambda並不需要替函數命名(如F(X) = X + 2, G(X) = F(X) + 3中的F、G便是函數的名稱)，所以我們常常把Lambda形容為「匿名的」(Anonymous)。


- Stream 使用說明

    在新的Java 8中，Collection提供了**stream()**方法，可以對集合做一些過濾和基本運算，而且這個當然也是有經過效能優化過的。

    除了**stream()**方法外還有**parallelStream()**方法，可以讓Collection各別針對它的entry另開出一個Thread，進行stream提供的運算，讓多核心的CPU資源更能有效的被利用。

- Stream 流中的匹配查找方法

    findAny：查找任何一個就返回Optional

    findFirst：查找到第一個就返回Optional

    anyMatch：匹配上任何一個則返回Boolean

    allMatch：匹配所有的元素則返回Boolean

    noneMatch : 跟allMatch相反，判斷條件裡的元素，所有的都不是，返回true

---

- 基本舉例 - 走訪

    過去我們使用For each走訪一個List可能要寫成這樣：
```Java
    for (String s : list) {
      System.out.print(s);
    }
```
    
    如果要走訪一個Map更麻煩，可能要寫成這樣：
```Java
    Set<String> keySet = map.keySet();
    for (String s : keySet) {
        System.out.print(s + ":" + map.get(s));
    }
```
    
    若改以Lambda來走訪這些Collection，可以簡化成這樣：
```Java
   list.forEach(s -> System.out.print(s));
```
```Java
   map.forEach((k, v) -> System.out.print(k + ":" + v));
```

- 基本舉例 - 基本運算/過濾
```Java
    List<String> list = new ArrayList<String>();
	list.add("1");
	list.add("2");
	list.add("3");
	list.add("5");
	list.add("4");
	List<String> list2 = list.stream()
                             .filter(s -> Integer.valueOf(s) < 3)
                             .collect(Collectors.toList());
	list2.add("7");
	list2.forEach(s -> System.out.print(s));
```
   
    以上會輸出「127」

- map ⇒ List中的物件轉換

    List<Card> cardList ⇒ List<CardDispModel>

    stream : 將原來資料打散

    map : 可將List中的物件做轉換

    collect : 最後再將物件收集起來
```Java
    private List<CardDispModel> genDispCardList(List<Card> cardList) {
        return cardList.stream().map(rs -> {
    		CardDispModel card = new CardDispModel();
    		// 隱碼後的卡號
    		card.setCardNoMask(DataMaskUtil.maskCreditCard(rs.getCardNo()));
    		// 卡片種類
    		card.setCardName(rs.getCardName());
    		// 鍵值
    		card.setCardKey(rs.getCardKey());
    		return card;
    	}).collect(Collectors.toList());
    }
```
- List<CustLoginInfo> ⇒ 回傳字串

    findFirst : 查找到第一個就返回Optional

    orElse : 否則回傳空字串
```Java
    List<CustLoginInfo> userData = userResource.getUserProfile(id, "", "");
    String custEmail = userData.stream()
                               .map(CustLoginInfo::getEmail)
                               .findFirst()
                               .orElse(StringUtils.EMPTY);
```
- noneMatch 使用
```Java
    boolean noneMatch = numbers.parallelStream()
                               .noneMatch(n -> StringUtils.equals(n, activityCode));
```
- peek ⇒ 通常用來call 沒有回傳任何型態的 method
```Java
    twAgreementOutAccounts = twAgreementOutAccounts.stream()
              .filter(this::isNoDayAmtData)
                        // 需再過濾無dayAmt的資料
    					.peek(this::setAccountInfoAndNickname) 
                        // 需組成帳戶資訊
    					.peek(AccountUtils::fillAcctName)
                        // 將空的accountName補上活期存款或支票存款
    					.collect(toList());
```
- reduce ⇒ 自訂運算需求
```Java
    List<DomesticAccountInfo> sourceAccountInfoList = demDepBalRs.getInfoList();
    sourceAccountInfoList.parallelStream()
                         .map(d -> NumberUtils.setScale(d.getCurrentBalTwd(), 2))
    					           .reduce(BigDecimal.ZERO, BigDecimal::add);
```
- groupingBy ⇒ 將List資料分組

    告訴它要用哪個當作分組的鍵（Key），最後傳回的Map結果會以List作為值（Value）
```Java
    Map<String, List<MfAcctDtlInqRsp>> groupedList = 
                                       resultList
                                      .stream()
                                      .collect(Collectors
                                      .groupingBy(MfAcctDtlInqRsp::getTrnDtYYYY));
```