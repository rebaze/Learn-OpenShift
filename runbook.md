> oc new-app -e POSTGRESQL_USER=luke \
             -e POSTGRESQL_PASSWORD=secret \
             -e POSTGRESQL_DATABASE=my_data \
             openshift/postgresql-92-centos7 \
             --name=my-database

> oc rollout status dc/my-database -w

> mvn package fabric8:deploy -Popenshift -Dmaven.test.skip

> oc rollout status dc/fruits -w

