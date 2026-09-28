# Learning Spark — VM Setup & Troubleshooting (XRDP • Hadoop • Spark)

> Replace `<username>` with your actual username.

## 1) Fix Remote Desktop Black Screen (XRDP + XFCE)

```bash
ssh <username>@cs6304-<username>-01.class.mst.edu
sudo apt update
sudo apt install -y xorgxrdp xfce4 xfce4-goodies dbus-x11
echo "startxfce4" > ~/.xsession
newgrp ssl-cert
rm -f ~/.Xauthority ~/.xsession-errors 2>/dev/null || true
sudo systemctl restart xrdp xrdp-sesman
sudo systemctl status xrdp xrdp-sesman --no-pager
```

## 2) Hadoop Smoke Test

```bash
stop-all.sh
hadoop namenode -format
start-all.sh
jps
```

Expected processes:

```
NameNode
DataNode
SecondaryNameNode
ResourceManager
NodeManager
Jps
```

**If DataNode is missing:**

```bash
start-dfs.sh
start-yarn.sh
```

Still missing? Reset state:

```bash
rm -r /tmp/hadoop-<username>/dfs/data/
stop-all.sh
hadoop namenode -format
start-all.sh
```

## 3) Spark Demo

Follow your course `commands.txt`. If Spark is not installed, use Section 4.

## 4) Install Spark (only if not installed)

```bash
sudo apt update
wget https://mirror.lyrahosting.com/apache/spark/spark-3.3.3/spark-3.3.3-bin-hadoop3.tgz
tar xvf spark-3.3.3-bin-hadoop3.tgz
rm spark-3.3.3-bin-hadoop3.tgz
sudo mv spark-3.3.3-bin-hadoop3/ /opt/spark
sudo tee /etc/profile.d/spark.sh <<'EOF'
export SPARK_HOME=/opt/spark
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
EOF
```

Log out of Remote Desktop, log back in, then:

```bash
spark-shell
# or
/opt/spark/bin/spark-shell
```

**If you see JNI / UnsupportedClassVersionError:**

```bash
sudo apt update
sudo apt install -y openjdk-17-jdk
printf '\n# Spark uses Java 17\nexport JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64\nexport PATH=$JAVA_HOME/bin:$PATH\n' >> ~/.bashrc
source ~/.bashrc
java -version
spark-shell
```

## 5) Run a Scala File in Spark

```bash
spark-shell
:load SimpleWordCount.scala
```

(Adjust paths as needed; ensure inputs exist if reading from HDFS.)

## 6) Replace the user_id

Replace <your_user_id> in the code and command with your id
