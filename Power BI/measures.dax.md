# Measures Documentation (DAX)

---
## Measure: M_実現損益

### DAX式
```dax
M_実現損益 = 
VAR CurrentBonds = CALCULATETABLE(
    VALUES('国内債券明細_時価評価'[銘柄])
    )
VAR PrevBonds = CALCULATETABLE(
    VALUES('国内債券明細_時価評価'[銘柄]),
    REMOVEFILTERS('C_カレンダー'[年月_yy年MM月]),
    TREATAS({[M_前Q年月_数値]}, 'C_カレンダー'[年月_数値])
    )
VAR RealizedBonds = EXCEPT(PrevBonds, CurrentBonds)
VAR RealizedTable = CALCULATETABLE(
    '国内債券明細_時価評価',
    REMOVEFILTERS('C_カレンダー'[年月_yy年MM月]),
    TREATAS({[M_前Q年月_数値]}, 'C_カレンダー'[年月_数値]),
    RealizedBonds
    )
RETURN
SUMX(RealizedTable, [M_評価損益])
```

---
## Measure: M_評価損益_前Q

### DAX式
```dax
M_評価損益_前Q = CALCULATE([M_評価損益],
                    REMOVEFILTERS('C_カレンダー'[年月_yy年MM月]),
                    TREATAS({[M_前Q年月_数値]}, 'C_カレンダー'[年月_数値]))
```

---
## Measure: M_評価損益_前Q比

### DAX式
```dax
M_評価損益_前Q比 = SUMX(VALUES('国内債券明細_時価評価'[銘柄]),
                     VAR Asset = '国内債券明細_時価評価'[銘柄]
                     VAR StartValue = CALCULATE([M_評価損益_前Q], '国内債券明細_時価評価'[銘柄] = Asset)
                     VAR EndValue = CALCULATE([M_評価損益], '国内債券明細_時価評価'[銘柄] = Asset)
           
                     RETURN IF(ISBLANK(EndValue), BLANK(),
                         IF(ISBLANK(StartValue), EndValue,
                             EndValue- StartValue)
                         )
                     )
```