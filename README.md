# QuotaClimat x Data For Good - ![badge](https://img.shields.io/endpoint?url=https://gist.githubusercontent.com/polomarcus/579237daab71afbb359338e2706b7f36/raw/test.json)


![](quotaclimat/utils/coverquotaclimat.png)
The aim of this work is to deliver a tool to a consortium around [QuotaClimat](https://www.quotaclimat.org/ "Quotaclimat website"), [Climat Medias](https://climatmedias.org/) allowing them to quantify the media coverage of the climate crisis. 

Radio and TV data are collected thanks to Mediatree API.

And webpress is currently at work in progress (as for 04/2024)

- 2022-09-28, Introduction by Eva Morel (Quota Climat): from 14:10 to 32:00 https://www.youtube.com/watch?v=GMrwDjq3rYs
- 2022-11-29 Project status and prospects by Estelle Rambier (Data): from 09:00 to 25:00 https://www.youtube.com/watch?v=cLGQxHJWwYA
- 2024-03 Project tech presentation by Paul Leclercq (Data) : https://www.youtube.com/watch?v=zWk4WLVC5Hs

## Index
- [I want to contribute! Where do I start?](#contrib)
- [Development](#wrench-development)
  - [File Structure](#file_folder-file-structure)
  - [Setting up the environment](#nut_and_bolt-setting-up-the-environment)

# 🤱 I want to contribute! Where do I start?

1. Learn about the project by watching the introduction videos mentioned above.
2. Create an issue or/and join https://dataforgood.fr/join and the Slack #offseason_quotaclimat.
3. Introduce yourself on Slack #offseason_quotaclimat

##  :wrench: Development

## Contributing

### :nut_and_bolt: Setting up the environment
Doing the following step will enable your local environement to be aligned with the one of any other collaborator.

First install pyenv:

<table>
<tr>
<td> OS </td> <td> Command </td>
</tr>

<tr>
<td> MacOS </td>
<td>

```bash
cd -
brew install pyenv # pyenv itself
brew install pyenv-virtualenv # integration with Python virtualenvsec
```
</td>
</tr>

<tr>
<td> Ubuntu </td>
<td>

```bash
sudo apt-get update; sudo apt-get install make build-essential libssl-dev zlib1g-dev \
libbz2-dev libreadline-dev libsqlite3-dev wget curl llvm \
libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev

curl https://pyenv.run | bash
```
</td>
</tr>

<tr>
<td> Windows </td>
<td>
An installation using miniconda is generally simpler than a pyenv one on Windows.
</td>
</tr>
</table>

Make the shell pyenv aware:

<table>
<tr>
<td> OS </td> <td> Command </td>
</tr>

<tr>
<td> MacOS </td>
<td>

```bash
eval "$(pyenv init --path)"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```

</td>
</tr>

<tr>
<td> Ubuntu </td>
<td>

```bash
export PYENV_ROOT="$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
```
</td>
</tr>

<tr>
<td> Windows </td>
<td>

:fr: Dans Propriétés systèmes > Paramètres système avancés >  Variables d'environnement...
Choisissez la variable "Path" > Modifier... et ajoutez le chemin de votre installation python, où se trouve le python.exe. (par défaut, C:\Users\username\AppData\Roaming\Python\Scripts\ )

:uk: In System Properties > Advanced >  Environment Variables...
Choose the variable "Path" > Edit... et add the path to your python's installation, where is located the pyhton.exe (by default, this should be at C:\Users\username\AppData\Roaming\Python\Scripts\ )

In the console, you can now try :
```bash
poetry --version
```

</td>
</tr>
</table>



Let's install a python version (for windows, this step have been done with miniconda):
```bash
pyenv install 3.11.6 # this will take time
```
Check if it works properly, this command:
```bash
pyenv versions
```
should return:
```bash
  system
  3.11.6
```

Then you are ready to create a virtual environment. Go in the project folder, and run:
```bash
  pyenv virtualenv 3.11.6 quotaclimat
  pyenv local quotaclimat
```

In case of a version upgrade you can perform this command to switch
```
eval "$(pyenv init --path)"
pyenv activate 3.11.6/envs/quotaclimat
```

You now need a tool to manage dependencies. Let's use poetry.
On windows, if not already installed, you will need a VS installation.

Link : https://wiki.python.org/moin/WindowsCompilers#Microsoft_Visual_C.2B-.2B-_14.x_with_Visual_Studio_2022_.28x86.2C_x64.2C_ARM.2C_ARM64.29

```bash
pip install poetry
poetry update
```
NLDA : I have not been able to work with wordcloud on windows. 

When you need to install a new dependency (use a new package, e.g. nltk), run 
```bash
poetry add ntlk
```
Update dependencies
```
poetry self update
```

After commiting to the repo, other team members will be able to use the exact same environment you are using. 

## Docker
First, have docker and compose [installed on your computer](https://docs.docker.com/compose/install/#installation-scenarios)

Then to start the different services
```
## To run only one service, have a look to docker-compose.yml and pick one service :
docker compose up metabase
docker compose up ingest_to_db
docker compose up mediatree
docker compose up test
```

If you add a new dependency, don't forget to rebuild
```
docker compose build test # or ingest_to_db, mediatree etc
```
### Explore postgres data using Metabase - a BI tool
```
docker compose up metabase -d
```
Will give you access to Metabase to explore the SQL table `sitemap table` or `keywords` here : http://localhost:3000/

To connect to it you have use the variables used inside `docker-compose.yml` :
* password: password
* username: user
* db: barometre
* host : postgres_db

#### Production metabase
If we encounter [a OOM error](https://www.metabase.com/docs/latest/troubleshooting-guide/running.html#heap-space-outofmemoryerrors), we can set this env variable : `JAVA_OPTS=-Xmx2g`

### Web Press - How to scrap 
The scrapping of sitemap.xml is done using the library [advertools.](https://advertools.readthedocs.io/en/master/advertools.sitemaps.html#)

A great way to discover sitemap.xml is to check robots.txt page of websites : https://www.midilibre.fr/robots.txt

What medias to parse ? This [document](https://www.culture.gouv.fr/Thematiques/Presse-ecrite/Tableaux-des-titres-de-presse-aides2) is a good start.

Learn more about [site maps here](https://developers.google.com/search/docs/crawling-indexing/sitemaps/news-sitemap?visit_id=638330401920319694-749283483&rd=1&hl=fr).

#### Scrap every sitemaps
By default, we use a env variable `ENV` to only parse from localhost. If you set this value to another thing that `docker` or `dev`, it will parse everything.

## Test
Thanks to the nginx container, we can have a local server for sitemap :
* http://localhost:8000/sitemap_news_figaro_3.xml

```
docker compose up -d nginx # used to scrap sitemap locally - a figaro like website with only 3 news
# docker compose up test with entrypoint modified to sleep
# docker exec test bash
pytest -vv --log-level DEBUG test # "test" is the folder containing tests
# Only one test
pytest -vv --log-level DEBUG -k detect
# OR
docker compose up test # test is the container name running pytest test
```

## Deploy
Every commit on the `main` branch will build an deploy to the Scaleway container registry a new image that will be deployed. Have a look to `.github/deploy-main.yml`.

Learn [more here.](https://www.scaleway.com/en/docs/tutorials/use-container-registry-github-actions/)

## Monitoring
With Sentry, with env variable `SENTRY_DSN`.

Learn more here : https://docs.sentry.io/platforms/python/configuration/options/

## Mediatree - Import data
Mediatree Documentation API : https://keywords.mediatree.fr/docs/

You must contact QuotaClimat team to 2 files with the API's username and password inside : 
* secrets/pwd_api.txt
* secrets/username_api.txt

Otherwise, a mock api response is available at https://github.com/dataforgoodfr/quotaclimat/blob/main/test/sitemap/mediatree.json

### Run
```
docker compose up mediatree
```

### Configuration - Batch import
### Based on time
If our media perimeter evolves, we have to reimport it all using env variable `START_DATE` like in docker compose (epoch second format : 1705409797).

Otherwise, default is yesterday midnight date (default cron job)

### Based on channel
Use env variable `CHANNEL` like in docker compose (string: tf1)

Otherwise, default is all channels

### Update without querying Mediatre API
In case we have a new word detection logic - and already saved data from Mediatree inside our DB (otherwise see Batch import based on time or channel) - we can re-apply it to all saved keywords inside our database.

⚠️ in this case, as we won't requery Mediatree API so we can miss some chunks, but it's faster. Choose wisely between importing/updating.

We should use env variable `UPDATE`  like in docker compose (should be set to "true")

In order to see actual change in the local DB, run the test first `docker compose up test` and then these commands :
```
docker exec -ti quotaclimat-postgres_db-1 bash
psql -h localhost --port 5432 -d barometre -U user
--> enter password : password
UPDATE keywords set number_of_keywords=1000 WHERE id = '71b8126a50c1ed2e5cb1eab00e4481c33587db478472c2c0e74325abb872bef6';
UPDATE keywords set number_of_keywords=1000 WHERE id = '975b41e76d298711cf55113a282e7f11c28157d761233838bb700253d47be262';
```

After having updated `UPDATE` env variable to true inside docker-compose.yml and running `docker compose up mediatree` you should see these logs : 
```
 update_pg_keywords.py:20 | Difference old 1000 - new_number_of_keywords 0
```

We can adjust batch update with these env variables (as in the docker-compose.yml): 
```
BATCH_SIZE: 50000 # number of records to update in one batch
NUMBER_OF_BATCH: 4 # number of batch size to process
```

### Batch program data
`UPDATE_PROGRAM_ONLY` to true will only update program metadata, otherwise, it will update program metadata and all theme/keywords calculations.

### Batch update from an offset
With +1 millions rows, we can update from an offset to fix a custom logic by using `START_OFFSET` to batch update PG from a offset. 

~55 minutes to update 50K rows on a mVCPU 2240 - 4Gb RAM on Scaleway.

Example inside the docker-compose.yml mediatree service -> START_OFFSET: 100

We can use [a Github actions to start multiple update operations with different offsets](https://github.com/dataforgoodfr/quotaclimat/blob/main/.github/workflows/scaleway-start-import-job-update.yml)

## SQL Tables evolution
Using [Alembic](https://alembic.sqlalchemy.org/en/latest/autogenerate.html) Auto Generating Migrations¶ we can add a new column inside `models.py` and it will automatically make the schema evolution :

```
# If changes have already been applied and you want to recreate your alembic file:
# 1. change to you main branch
# 2. start test container and run "pytest -vv -k api" to rebuild the state of the DB (or drop table the table you want)
# 3. rechange to your WIP branch 
# 4. connect to the test container : docker compose up test -d / docker compose exec test bash
# 5. reapply the latest saved state : poetry run alembic upgrade head
# 6. Save the new columns
poetry run alembic revision --autogenerate -m "Add new column test for table keywords"
# this should generate a file to commit inside "alembic/versions"
# 7. to apply it we need to run, from our container
poetry run alembic upgrade head
```

Inside our Dockerfile_api_import, we call this line 
```
# to migrate SQL tables schema if needed
RUN alembic upgrade head
```
### Channel metadata
In order to maintain channel perimeter (weekday, hours) up to date, we save the current version inside `postgres/channel_metadata.json`, if we modify this file the next deploy will update every lines of inside Postgresql table `channel_metadata`.

## Keywords
## Produce keywords list from Excel files
How to update `quotaclimat/data_processing/mediatree/keyword/keyword.py` from shared excel files ?
Download files locally then :
```
poetry run python3 quotaclimat/transform_excel_to_json.py > cc-bio.json
# then update quotaclimat/data_processing/mediatree/keyword/keyword.py list
```

## Program Metadata table
The media perimeter is defined here : "quotaclimat/data_processing/mediatree/channel_program.json"

To calculate the right total duration for each channel, after updating "quotaclimat/data_processing/mediatree/channel_program.json" you need to execute this command to update `postgres/program_metadata.json` 
```
poetry run python3 transform_program.py
```
The SQL queries are based on this file that generate the Program Metadata table.

**With the docker-entrypoint.sh this command is done automatically, so for production uses, you will not have to run this command.**

## Production monitoring
* Use scaleway
* Use [Ray dashboard] on port 8265

### Fix linting
Before committing, make sure that the line of codes you wrote are conform to PEP8 standard by running:
```bash
poetry run black .
poetry run isort .
poetry run flake8 .
```
There is a debt regarding the cleanest of the code right now. Let's just not make it worth for now.

## Thanks
* [Eleven-Strategy](https://www.welcometothejungle.com/fr/companies/eleven-strategy)
