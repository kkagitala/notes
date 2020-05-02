---
layout: post
title:  "Notes I got from Max D"
date:   2020-04-20
excerpt: "Thanks to Max.D I was able to get this handy notes from him"
---

¯\_(ツ)_/¯
# kibana
external_api:checkout AND action:charge AND status:FAILED AND "internal server error"

https://logs.patreontools.com/_plugin/kibana/app/kibana#/discover?_g=(refreshInterval:(display:Off,pause:!f,value:0),time:(from:now-90d,mode:quick,to:now))&_a=(columns:!(_source),index:e902eac0-7423-11e8-8555-ef5c87a05a51,interval:auto,query:(language:lucene,query:'external_api:checkout%20AND%20action:charge%20AND%20status:FAILED%20AND%20%22internal%20server%20error%22'),sort:!(ts,desc))

# apm trace
https://app.datadoghq.com/apm/resource/patreon.daemon.payments-worker/patreon.daemon_framework.worker_daemon.do_work/7fb0f0fa80c82952?end=1586486943289&env=production&graphType=flamegraph&index=apm-search&paused=false&query=%40patreon.uuid%3A5ed58f6a-12be-4f5a-8af9-9cf3f5ff830d&start=1586483343289


verification email: verification_factor_email
https://www.patreon.com/auth/verify-device?token=7Aw24OTde2FO3WGMDUl8Ss1n-zc5kLurV3g7s_OfhA4&u=32187

# Main
```
mysql-main-production-writer.internal.patreon.com

mysql-main-production-reader.internal.patreon.com

mysql-payments-production-writer.internal.patreon.com

mysql-payments-production-reader.internal.patreon.com
```

while true; do devx sync; sleep 5; done
PMM

# restart mysql serverfault
 sudo /etc/init.d/mysql restart

/etc/datadog-agent/conf.d/mysql.d/check.yaml

### devx run single test 
devx pytest -- test/app/api/routes/test_payment.py::TestGetPayments::test_get_filtered_payments
PATREON_PY_CONFIG_PATH=/etc/patreon/testing.yml py.test /opt/code/patreon_py/file_name_py

devx run script in test mode locally
PATREON_PY_CONFIG_PATH=/etc/patreon/testing.yml patreon_repl file_name_py

in a prod scripts box 

show provision logs
less /var/log/provision.log 

show all logs
journalctl -e | less

./launchbot.py -e production -s payments-worker:24

./launchbot.py -e development -s partitioned-queuer:2
./deploybot.py -e development -g python.partitioned-queuer 'LOAD-TEST-20191122-01'


show all payments-* logs
journalctl -u cron* --since "2019-08-05"  | less
journalctl -u cron-runner* --since "2019-08-05"  | less
journalctl -u session* --since "2020-04-09"  | less


journalctl -u septoria* --since "2020-04-20"  | less
journalctl -u payments* --since "2020-04-21"  | less
journalctl -u partitioned* --since "2019-11-27" | less
journalctl -u claus-external* | less
journalctl -u argus* | less
journalctl -u rollups-worker* --since "2019-06-03" | less


Enable debug logging gunicorn
------------------------------
--log-level debug

vim /etc/systemd/system/patreonweb.service
sudo systemctl daemon-reload
sudo systemctl restart patreonweb


show all payments workers running
./ec2_server_list.py | egrep '(partitioned-queuer|payments-worker|payment-response-worker|rollsup|webapi).*running.*x-development'
./ec2_server_list.py | egrep '(partitioned-queuer|payments-worker).*running.*x-production'
./ec2_server_list.py | egrep '(crons-prod).*running.*x-production'
./ec2_server_list.py | egrep '(septoria).*running.*x-production'

./ec2_server_list.py | egrep '(rollups).*running.*x-production'

deploy to all
./deploybot.py -e development -g python.partitioned-queuer,payments-worker,payment-response-worker load-test-dec-2019

KILL a payments workers
./launchbot.py -ri partitioned-queuer:i-055d5e764918856d2
./launchbot.py -ri payments-worker:i-0e55242f19a55cd73
./launchbot.py -ri argus-production:i-0558271e058b4c67d
./launchbot.py -ri claus-external:i-0cf8f6946bdb4a6d7
./launchbot.py -ri claus-internal:i-04a85fcb6a534144f
./launchbot.py -ri pubsub-auditor:i-0ee36b200a1d8e9f3
./launchbot.py -ri webapi:i-051562c3aa8f751b6

./launchbot.py -ri prod-scripts-honeybadger902:i-0082e3315fab4b065

deploy to specific machines
./deploybot.py <your sha> -g python.rollups-worker


set count 
./launchbot.py -s payments-worker:4
./launchbot.py -s partitioned-queuer:2
./launchbot.py -s claus-internal:4
./launchbot.py -s claus-external:4
./launchbot.py -s rollups-worker:12
./launchbot.py -s rollups-cleaner:4

./launchbot.py -s webapi:50

./launchbot.py -s claus-external:1 -e development
./launchbot.py -s payments-worker:1 -e development
./launchbot.py -s partitioned-queuer:0 -e development

provision a worker
./launchbot.py -a payments-worker:23 -e development
./launchbot.py -a partitioned-queuer:3 -e development
./launchbot.py -a claus-internal:4

restart service
sudo systemctl restart partitioned-queuer.service
sudo systemctl restart payments-worker.service
sudo systemctl restart prod-scripts.service

./deploybot.py --fast-operations restarts -g python.payments-worker

Rebuild / relaunch
deploybot will reprovision the hosts, updating the ansible and patreon_py branches
./deploybot.py -g python.partitioned-queuer,payments-worker -e development

launchbot --rebuild will tear down all of the machines and launch new ones
./launchbot.py --rebuild  payments-worker -p <your ansible branch> -e development


SQL stuff
----------
sql alchemy verbose logging
import logging
logging.basicConfig()
logging.getLogger('sqlalchemy.engine').setLevel(logging.INFO)

ALTER USER 'payments'@'%' IDENTIFIED BY 'AM6xT4yaHfPVuDKHhtsgQC3ECf3BdV8T';

select count(*) from bills where status = 'pending' created_at = '2019-03-01 07:59:59'

select * from txns t where t.failed_at is not null and t.created_at >= '02-19-2019' order by txn_id desc limit 10

select count(*) from txns where state = 'queued'
select * from bill_payments where txn_id = 113125304
select * from txns where txn_id = 113125304

select * from txns where txn_id = 114005719

show open tables from patreon where In_use = true

show processlist

SELECT * FROM information_schema.processlist WHERE `STATE` LIKE 'waiting%';

select CONCAT('CALL mysql.rds_kill(', ID, ');')  from INFORMATION_SCHEMA.PROCESSLIST where State like 'Waiting for table metadata lock%' and User = 'patreonapp' order by TIME DESC;


select CONCAT('CALL mysql.rds_kill(', ID, ');')  from INFORMATION_SCHEMA.PROCESSLIST where Command = 'Sleep' order by TIME DESC;

select * from txns where txn_id in (110406886, 110893532)

select * from transaction_state_history_log where txn_id = 110893532
CALL mysql.rds_kill(6567)

SHOW ENGINE INNODB STATUS

# show all open txns
SELECT * FROM information_schema.innodb_trx


show variables

select count(distinct left(host,instr(host,':')-1)) from processlist ;
select count(*), left(host,instr(host,':')-1) from processlist group by left(host,instr(host,':')-1) order by count(*) desc limit 20;

select INDEX_NAME, COUNT_STAR, round(SUM_TIMER_WAIT/pow(10,12),1) stw_s
from performance_schema.table_io_waits_summary_by_index_usage
where OBJECT_SCHEMA='patreon' and OBJECT_NAME='bills' order by count_star desc limit 100;

check long runnign queries
SELECT ID, USER, LEFT(HOST, ( LOCATE( ':', HOST ) - 1 ) ) AS HOST, COMMAND, TIME, REPLACE(SUBSTRING(info,1,50),"\n","") info_50 FROM INFORMATION_SCHEMA.PROCESSLIST WHERE COMMAND NOT LIKE 'Binlog Dump%' and COMMAND NOT LIKE 'Sleep' ORDER BY TIME DESC LIMIT 20;


show status where `variable_name` = 'Threads_connected';

SHOW GLOBAL STATUS;


Show framentation  of tables
./launchbot.py -ri argus-production:i-0484780d00371648f
https://serverfault.com/questions/202000/how-find-and-fix-fragmented-mysql-tablesz


Percona monitoring
centos@172.31.12.209


alias patreon_repl='PYTHONPATH=/opt/code/patreon_py /opt/code/patreon_py/venv/bin/python'


