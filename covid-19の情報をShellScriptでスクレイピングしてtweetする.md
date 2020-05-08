:title covid-19の情報をShellScriptでスクレイピングしてtweetする
:create 2020-05-08 17:01:28

:body
covid-19が収まらないのでShellScriptで札幌市のページをスクレイピングして  
Twitterに流すことにしました。  

```
#!/bin/sh

HTML=$(curl -sS "https://www.city.sapporo.jp/hokenjo/f1kansen/2019n-covhassei.html")
DIR=/home/x6d61


TABLE=$(printf "%s" "${HTML}"                                                                    |
awk '
BEGIN{
    i=0
}
{
    html[NR]=$0;
    if(match($0,"table")) {
        table_index[i]=NR
        i++
    }
}
END {
    for(i=table_index[0];i<table_index[1];i++) {
        print html[i]
    }
}')
TITLE=$(printf "%s" "${HTML}"                                                             |
grep -o "<h2>.*</h2>"                                                                     |
sed 's/<h2>\(.*\)<\/h2>/\1/'                                                              |
sed 's/市/札幌市/')
echo $TITLE

INFECTED_PERSON_TABLE=$(printf "%s" "${TABLE}"                                            |
grep -oE '<th .*>.*</th>'                                                                 |
tr -d '0-9a-z="/<>_%'                                                                     |
xargs -o)          

INFECTED_PERSON=$(printf "%s" "${TABLE}"                                                  |
grep -oE '<span class="txt_big">.*</span>'                                                |
tr -d 'a-z="/<>_'                                                                         |
xargs -o)
awk  -v INFECTED_PERSON="$INFECTED_PERSON" -v INFECTED_PERSON_TABLE="$INFECTED_PERSON_TABLE"  'BEGIN{
    split(INFECTED_PERSON,infected_person_array," ")
    split(INFECTED_PERSON_TABLE,infected_person_table_array," ")
    for(i=0;i<=4;i++) {
        printf("%s %s\n",infected_person_table_array[i],infected_person_array[i])
    }
}
'

INFECTED_PERSON_COUNT=$(echo $INFECTED_PERSON | grep -oE "^[0-9]{1,3}")
INFECTED_PERSON_BEFORE=$(cat $DIR/covid19)
DATE=$(echo $TITLE | grep -oE "[0-9]{1,2}月[0-9]{1,2}日")
echo
echo "${DATE}の陽性患者 $(($INFECTED_PERSON_COUNT-$INFECTED_PERSON_BEFORE))人"
echo $INFECTED_PERSON_COUNT > $DIR/covid19
```

tweetする部分は日本シェルショッカー日本支部様の小鳥男を利用しました。  
あとはこれをcronに登録して  

```
0 10 * * * /home/ubuntu/covid19.sh | /home/ubuntu/kotoriotoko/BIN/tweet.sh
```

ShellScript便利！
