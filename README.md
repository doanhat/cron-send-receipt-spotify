# Cron-send-facture-spotify

[![hackmd-github-sync-badge](https://hackmd.io/DlraECW4Q_imvwXGCAM1Ag/badge)](https://hackmd.io/DlraECW4Q_imvwXGCAM1Ag)


A Python job (scheduled by Crontab at the end of every month) which retrieves from Gmail the lastest billing of Spotify Family and notifies all family members within a Facebook Messenger group.

## Prerequisites

- Services:
    - Gmail + Facebook account 
    - GCP
- Authentification:
    - Gmail API enabled
    - OAuth 2.0 Client IDs generated
- Tools:
    - Poetry >= 1.1.10
    - Python >= 3.7.2
    - Docker latest
    

## Installation

### 1. Clone the repository

With Terminal:

```bash=
  git clone git@github.com:doanhat/cron-send-facture-spotify.git
  cd cron-send-facture-spotify/
```
With Pycharm:

> Open `Pycharm` -> `GET FROM VCS` -> Copy and paste the git url in the field `URL`

### 2. Install dependencies:

Open Terminal (system or Pycharm), **within the repository folder**:

```bash
  poetry install
```
:warning: In Pycharm, remember to config interpreter to choose the virtual environment generated by Poetry

:warning: If some dependecy problems occur, delete the `poetry.lock` file and retry the above command

### 3. Check with test script before heading to the cron job:

in MacOs, **within the repository folder**:

1. Make sure the test sript is executable:
```bash
  chmod +x app/test/cron/test-cron.sh
```
2. Run the script:
```bash
  app/test/cron/test-cron.sh
```
3. Verify:
- It should log an `info` indicating the current time, for example:
    > `INFO: Current Time = 10:46:03`
- It should write (or create if not exists) to `app/test/cron/test.txt` the log content above, for example:
    > Current Time = 23:12:56 <br/>
    > Current Time = 11:54:49 <br/>
    > Current Time = 11:56:49 <br/>
    > Current Time = 12:26:48 <br/>
    > Current Time = 10:46:03

:warning: If `command not found`: 

- Change the command, which might vary depending on the OS system
- If running from **root** in Terminal, use the **absolute path**

4. Run a simple cron job:

```bash
  crontab -e
```
 - in Vim editor, add the following line:
```
  * * * * * <absolute_path_workdir>/app/test/cron/test-cron.sh

```
An example of `<absolute_path_workdir>`:
> `/Users/ndoan/PersoProj/cron-send-facture-spotify`
 - save and quit Vim

Now, the cron job should run the `test-cron.sh` script every minute which get the current time and write it to `test.txt`, then crontab will send an email to the user with all the logs. By default, the user email is local which can be checked from the Terminal by the command `mail`.

5. Check the results and turn off the cron job by adding a `#` before the job line or delete it

### 4. Set up the cron job !!

1. Set up authentification:
    1. Google API credentials for Gmail readonly access:
        - Create a new project in `Google Cloud Console`
        - In `Home/APIs and Services`, enable Gmail API
        - In `Home/APIs and Services/OAuth Consent Screen`, enter application name and add your email to `Test users`
        - In `Home/APIs and Services/Credentials`, create a `OAuth 2.0 Client ID` and download the JSON file, save it to `app/main/config/google/API/credentials.json`
    3. Facebook messenger login:
        - Define environment variables:
            - `FB_USER_EMAIL_ADDRESS`=<fb_email_address>
            - `FB_USER_PASSWORD`=<fb_password>
            - `FB_THREAD_ID`=<messenger_group_id>
            - :warning: `FB_THREAD_ID` might not work (bug?), in this case it can be passed directly when calling `docker run`
                > :information_source: `<messenger_group_id>` example: `6947468915279299` is the id of https://www.facebook.com/messages/t/6947468915279299

2. Build image Docker:
- In Terminal (system or Pycharm), **within the repository folder**:

```bash
  docker build --no-cache -t cron-send-facture-spotify -f docker/Dockerfile .
```

3. Change environment variable in docker command:
- In `send-facture-spotify.sh`:
```shell=15
  /usr/local/bin/docker run -e FB_USER_EMAIL_ADDRESS="$FB_USER_EMAIL_ADDRESS" -e FB_USER_PASSWORD="$FB_USER_PASSWORD" -e FB_THREAD_ID=6947468915279299 cron-send-facture-spotify:latest
```
- Change `FB_THREAD_ID=6947468915279299` to desired value:
    - `"$FB_THREAD_ID"` if `FB_THREAD_ID` already defined as environment variable
    - `<messenger_group_id>` if `"$FB_THREAD_ID"` not working
5. Schedule Crontab:

- In crontab Vim editor, add the following job:

```bash
  59 10 28-31 * * <absolute_path_workdir>/app/main/cron/send-facture-spotify.sh
```
```cmake
  which means: At 10:59 on every day-of-month from 28 through 31, run the script
```

:information_source: Check out [crontab.guru](https://crontab.guru/#59_10_28-31_*_*) for more cron schedule expression

### 5. Voilà, all set !!

## Author
- [@doanhat](https://github.com/doanhat)

## Links
[![linkedin](https://img.shields.io/badge/linkedin-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/minhdoan272/)
[![facebook](https://img.shields.io/badge/Facebook-1877F2?style=for-the-badge&logo=facebook&logoColor=white)](https://www.facebook.com/dnminhhhhh/)
[![gmail](https://img.shields.io/badge/Gmail-D14836?style=for-the-badge&logo=gmail&logoColor=white)](nhatminhdoan2702@gmail.com)
## License

[![mit](https://img.shields.io/badge/License-MIT-blue.svg)](https://choosealicense.com/licenses/mit/)

## Reference:

[Read Emails from Gmail using Gmail API](https://www.geeksforgeeks.org/how-to-read-emails-from-gmail-using-gmail-api-in-python/)

[Gmail API - Quick Started](https://developers.google.com/gmail/api/quickstart/python)

[Fbchat documentation](https://fbchat.readthedocs.io/en/stable/)

