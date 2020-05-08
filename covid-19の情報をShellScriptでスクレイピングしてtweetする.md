:title covid-19の情報をShellScriptでスクレイピングしてtweetする
:create 2020-05-08 17:07:34
:create 2020-05-08 17:01:28

:body
covid-19が収まらないのでShellScriptで札幌市のページをスクレイピングして  
Twitterに流すことにしました。  

```
#!/bin/sh

HTML=$(curl -sS "https://www.city.sapporo.jp/hokenjo/f1kansen/2019n-covhassei.html")
DIR=/home/x6d61


TABLE=$(printf "" "${HTML}"                                                                    |
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
TITLE=$(printf "" "${HTML}"                                                             |
grep -o "<h2>.*</h2>"                                                                     |
sed 's/<h2>\(.*\)<\/h2>//'                                                              |
sed 's/市/札幌市/')
echo $TITLE

INFECTED_PERSON_TABLE=$(printf "" "${TABLE}"                                            |
grep -oE '<th .*>.*</th>'                                                                 |
tr -d '0-9a-z="/<>_