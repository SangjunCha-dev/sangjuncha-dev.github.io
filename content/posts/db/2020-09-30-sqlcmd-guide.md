---
title: "sqlcmd 사용법 (sqlcmd-guide)"
date: 2020-09-30T14:41:18+09:00
description: sqlcmd 사용법 설명
# menu:
#   sidebar:
#     name: sqlcmd-guide
#     identifier: sqlcmd-guide
#     parent: db
#     weight: 30
tags: ["db", "sqlcmd"]
categories: ["DB", "sqlcmd"]
---



# sqlcmd

사용목적 : 대용량 sql 파일실행은 `sqlcmd`만 실행가능

### sqlcmd 사용 예시

```bash
sqlcmd \
-S 127.0.0.1,1433 \
-i input_file[ , input_file2...] \
-o output_file \
-U login_id \
-P password \
-d db_name
```

> -S : 접속할 IP,접속포트  
> -i : 실행할 sql 파일 지정  
> -i C:\<filename>  
> -i \\<Server>\<Share$>\<filename>  
> -i "C:\Some Folder\<file name>"  
> -o : 실행결과 저장할 파일 지정  
> -U : 계정  
> -P : 패스워드  
> -d : Database 이름  

windows 환경
- 계정과 패스워드는 설정해주지 않으면 윈도우 기본계정으로 접속

linux 환경에서 대용량 파일 줄단위 분할
- split -l 10000 "aaa.sql" "aaa.sql."

### sqlcmd 옵션

```bash
sqlcmd
   -a packet_size
   -A (dedicated administrator connection)
   -b (terminate batch job if there is an error)
   -c batch_terminator
   -C (trust the server certificate)
   -d db_name
   -e (echo input)
   -E (use trusted connection)
   -f codepage | i:codepage[,o:codepage] | o:codepage[,i:codepage]
   -g (enable column encryption)
   -G (use Azure Active Directory for authentication)
   -h rows_per_header
   -H workstation_name
   -i input_file
   -I (enable quoted identifiers)
   -j (Print raw error messages)
   -k[1 | 2] (remove or replace control characters)  
   -K application_intent  
   -l login_timeout  
   -L[c] (list servers, optional clean output)  
   -m error_level  
   -M multisubnet_failover  
   -N (encrypt connection)  
   -o output_file  
   -p[1] (print statistics, optional colon format)  
   -P password  
   -q "cmdline query"  
   -Q "cmdline query" (and exit)  
   -r[0 | 1] (msgs to stderr)  
   -R (use client regional settings)  
   -s col_separator  
   -S [protocol:]server[instance_name][,port]  
   -t query_timeout  
   -u (unicode output file)  
   -U login_id  
   -v var = "value"
   -V error_severity_level
   -w column_width
   -W (remove trailing spaces)
   -x (disable variable substitution)
   -X[1] (disable commands, startup script, environment variables, optional exit)
   -y variable_length_type_display_width
   -Y fixed_length_type_display_width
   -z new_password
   -Z new_password (and exit)
   -? (usage)
```