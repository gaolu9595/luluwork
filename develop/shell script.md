

# Shell Script

[TOC]

## >/dev/null

将输出重定向到一个不存在的文件中，即丢弃不需要的输出流

```shell
#!/bin/bash
#!/bin/bash
CLICKHOUSE="clickhouse"
DAILYREPORT="daily-report"
if [ $# == 2 ]; then
    machine=$1
    id=$2
    suffix="-fw-us-east.stickyadstv.com"
    user="backup"
    if [ ${machine} == ${CLICKHOUSE} ]; then
    	suffix=".stickyadstv.com"
	user="root"
    	host_name="${machine}${id}${suffix}"
    	echo "===ssh $user@$host_name==="
    	ssh $user@$host_name
    elif [ ${machine} == ${DAILYREPORT} ]; then
        user="root"
        host_name="${machine}${id}${suffix}"
        echo "===ssh $user@$host_name==="
        ssh $user@$host_name
    else
	host_name="${machine}${id}${suffix}"
        echo "===ssh $user@$host_name==="
        ssh $user@$host_name
    fi
fi
echo All subshells finished
```

```shell
#!/bin/bash
#!/bin/bash
#PREFIX="redstats"
PREFIX="mapstats"
SUFFIX="-fw-us-east.stickyadstv.com"
#MAX_ID=36
MAX_ID=300
USER="backup"
if [ $# == 0 ] ; then
    for index in $(seq 1 $MAX_ID)
    do
        echo "========================================="
        host_name="${PREFIX}${index}${SUFFIX}"
        echo "$host_name"
        echo "========================================="
        ssh $USER@$host_name 'grep --color "ERROR \[" /var/log/mapstats/2021*.log'
    done
elif [ $# == 1 ]; then
    command=$1
    for index in $(seq 1 $MAX_ID)
    do
        echo "========================================="
        host_name="${PREFIX}${index}${SUFFIX}"
        echo "$host_name"
        echo "run [$command]"
        echo "========================================="
        ssh $USER@$host_name "$command"
    done
elif [ $# == 2 ]; then
    index=$1
    command=$2
    echo "========================================="
    host_name="${PREFIX}${index}${SUFFIX}"
    echo "$host_name"
    echo "run [$command]"
    echo "========================================="
    ssh $USER@$host_name "$command"
fi
echo All subshells finished
```

```shell
#!/bin/bash
# PLA
# TicketID=OPS-79179
# CollectD plugin to count errors in redstats
# - Can run on all servers, but do nothing if concerned redstats are not presents
# - Find only last file(s) with last modify < $mmin min
hostname=$(hostname)
interval=3600		# Interval for GROUP BY
loopInterval=600	# In seconds. Loop interval
mmin="-180"
logFileName="server.log*"
logFileDir="/var/log/redstats/*"
countErrors() {
	files=$(find ${logFileDir} -name "${logFileName}" -mmin ${mmin} 2>/dev/null)
	#echo -e "FILES:\n $files"
	if [[ -z "${files}" ]]; then
		return
	fi
	while read file; do
		rsName=$(echo "$file" | sed -nr 's@.*/(.*)/server.log.*@\1@p')
		fileName=$(basename "$file")
		if [[ "$fileName" == "server.log" ]]; then
			fileDate=$(date -d now +'%Y-%m-%d-%H')
		else
			fileDate=$(echo "$fileName" | cut -d'.' -f3)
		fi
		ts=$(date -d "$(echo "${fileDate}" | sed -r 's/(.*)-/\1 /')" +%s)
		while read -r line ; do
		    count=$(grep -c "${line}" "${file}" 2>/dev/null)
			if [[ ${count} -ne 0 ]]; then
				error=$(echo ${line} | sed "s/ /_/g")
				echo -e "PUTVAL \"${hostname}/redstats_errors_${error}/gauge-${rsName}\" interval=${interval} ${ts}:${count}"
			fi
		done < <(grep -o "ERROR [a-zA-Z ]*[,.:;(=]" "${file}" | awk -F ' for | with | in | on ' '{sub(/[,.:;[(=]/, "", $1);print substr($1,7)}' | sort | uniq)
	done <<< "${files}";
}
while :; do
	countErrors
	sleep ${loopInterval}
done
exit 1
```

