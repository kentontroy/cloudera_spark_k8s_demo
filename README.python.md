### Install and configure Anaconda

```
[centos@cdp ~]$ wget https://repo.anaconda.com/archive/Anaconda3.2020.02-Linux-x86_64.sh
[centos@cdp ~]$ chmod +x ./Anaconda3-5.3.1-Linux-x86_64.sh

Chose to install Anaconda in /home/centos/anaconda3

The installer places the following in .bashrc

__conda_setup="$(CONDA_REPORT_ERRORS=false '/home/centos/anaconda3/bin/conda' shell.bash hook 2> /dev/null)"
if [ $? -eq 0 ]; then
    \eval "$__conda_setup"
else
    if [ -f "/home/centos/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/home/centos/anaconda3/etc/profile.d/conda.sh"
        CONDA_CHANGEPS1=false conda activate base
    else
        \export PATH="/home/centos/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup

[centos@cdp ~]$ source .bashrc

[centos@cdp ~]$ which python3
~/anaconda3/bin/python3

[centos@cdp ~]$ python3 --version
Python 3.7.0

[centos@cdp ~]$ which pip3
~/anaconda3/envs/streamlit/bin/pip3
```

### Install Python package dependencies

```
[centos@cdp ~]$ conda create --name streamlit python=3.7
[centos@cdp ~]$ conda activate streamlit
(streamlit) [centos@cdp ~]$ pip3 install streamlit
(streamlit) [centos@cdp ~]$ pip3 install impyla
(streamlit) [centos@cdp ~]$ conda upgrade pandas
(streamlit) [centos@cdp ~]$ conda install pystan
(streamlit) [centos@cdp ~]$ conda install fbprophet

The image used in this Github repo was already enabled with Kerberos.
See https://docs.cloudera.com/runtime/7.2.7/impala-start-stop/topics/impala-impyla.html
if additional dependencies are needed. For this repo, only do

(streamlit) [centos@cdp src]$ pip3 install impyla[kerberos]
```
### Test connectivity to Impala

```
(streamlit) [centos@cdp src]$ kinit -kt /etc/security/keytabs/etl_user.keytab etl_user/$(hostname -f)@CLOUDERA.COM

Create a file, ImpalaTest.py, with contents similar to:
------------------------------------------------------------------
from impala.dbapi import connect
from impala.util import as_pandas

# Background reference
# https://github.com/cloudera/impyla

IMPALA_HOST = "cdp.cloudera.com"
IMPALA_PORT = 21050
IMPALA_USER = "etl_user"
IMPALA_AUTH = "GSSAPI"
KUDU_TABLE = "default.bpd_crime_data"

conn = connect(host=IMPALA_HOST, port=IMPALA_PORT, user=IMPALA_USER, auth_mechanism=IMPALA_AUTH)
cursor = conn.cursor()
cursor.execute("SELECT * FROM {0} LIMIT 100".format(KUDU_TABLE))
df = as_pandas(cursor)
print(df)

(streamlit) [centos@cdp src]$ python3 ImpalaTest.py

```
### Run Streamlit app
```
(streamlit) [centos@cdp src]$ streamlist run AppStreamlitGui.py
```




