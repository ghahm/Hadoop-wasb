# Hadoop VM에서 wasb 연결 설정하기

1. Azure Marketplace에서 Hadoop VM presented by Bitnami 생성하기
   * 마켓 플레이스에서 Hadoop으로 검색
   * Bitnami에서 제공한 VM 템플릿을 이용하여 VM 생성하기

2. Azure Storage 관련 jar를 환경 변수로 등록하기
   * cd /opt/bitnami/hadoop/etc/hadoop
   * sudo vi hadoop-env.sh
   * hadoop-env.sh 파일 맨 마지막에 아래 내용을 추가
   * export HADOOP_CLASSPATH=$HADOOP_PREFIX/share/hadoop/tools/lib/hadoop-     azure2.7.3.jar:$HADOOP_PREFIX/share/hadoop/tools/lib/azure-storage-2.0.0.jar:$HADOOP_CLASSPATH

3. Azure Storage Account Key 등록하기
   * cd /opt/bitnami/hadoop/etc/hadoop
   * sudo vi core-site.xml
   * core-site.xml 파일 맨 마지막 `</configuration>` 위에 내용을 추가
   * `<property>`
   * `<name>`fs.azure.account.key.[Storage Account Name].blob.core.windows.net`</name>`
   * `<value>`[Storage Account Key]`</value>`
   * `</property>`

4. 재시작하기
   * sudo /opt/bitnami/ctlscript.sh restart

5. 테스트하기
   * hadoop fs -cat wasb://[Container Name]@[Storage Account Name].blob.core.windows.net/[Test File Name] | grep [Test Word] > [Target File Name] | hadoop fs -put -f [Target File Name] wasb://[Container Name]@[Storage Account Name].blob.core.windows.net/[Target File Name]

### 주의사항 <br>
(1) - Azure Blob Storage의 컨테이너 내에 특정 디렉토리 밑에 파일을 저장해야 할 경우, 해당 디텍토리가 없으면 에러가 발생
1. hadoop fs -put -f xxx.json wasb://~~와 같이 Azure Blob Storage의 컨테이너 내에 특정 디렉토리 밑에 파일을 저장해야 할 경우, 해당 디텍토리가 없으면 에러가 발생
2. 이런 경우 hadoop fs -mkdir wasb://[Container Name]@[Storage Account Name].blob.core.windows.net/[리텍토리명]와 같이 mkdir을 실행한 후 put을 실행할 것

### 참고사항
1. wasb 연결을 위해서는 azure-storage-[version].jar과 hadoop-azure-[version].jar 파일이 필요하며 Bitnami Hadoop VM의 경우 해당 파일의 위치는 /opt/bitnami/hadoop/share/hadoop/tools/lib 디텍토리임
