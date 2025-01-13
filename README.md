## Quick Start
```
cd db
wget https://github.com/AppertaFoundation/openeyes-sample-db/raw/refs/heads/release/v6.8.0/sql/sample_db.zip
mv sample_db.zip sample_db.gz
gzip -dc sample_db.gz > sample_db.sql
rm -rf sample_db.gz
cd ..
docker compose up --buiid

```

[More Info](https://github.com/AppertaFoundation/openeyes/wiki/Installation-Instructions-v6.0)