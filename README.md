Backup wp-content:
docker run --rm --volumes-from cms -v $(pwd)/wp-content:/backup ubuntu tar cvf /backup/wp-content.tar /var/www/html/wp-content
Restore wp-content:
docker run --volumes-from cms -v $(pwd)/wp-content:/backup ubuntu bash -c "cd /var && tar xvf /backup/wp-content.tar --strip 1"
Backup DB
docker exec db sh -c 'exec mysqldump -uroot -p"$MYSQL_ROOT_PASSWORD" cms' | gzip -9 > ./cms_db.sql.gz
Restore DB
gzip -d < cms_db.sql.gz | docker exec -i db sh -c 'exec mysql -uroot -p"$MYSQL_ROOT_PASSWORD" cms'
