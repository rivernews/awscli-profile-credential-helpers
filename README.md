# AWS CLI Profile Credential Helpers for ClinGen

[Upstream github repo](https://github.com/aws-samples/awscli-profile-credential-helpers).

The SSO command requires AWS CLI version 2, which is not available through package managers like `pip`. We'll use a dispensable docker container for AWS CLI to minimize impact on your system file especially you have AWS CLI version 1 installed and haven't migrated to version 2 yet.

## Install

1. Make sure your python is later version e.g. later than 3.7
1. Create a virtual environment, activate it, and install dependencies in `requirements.txt`

## How to create & refresh SSO credentials

1. You need docker running. If you don't have docker yet, assume you're on Macos, install docker by `brew cask install docker`.
2. Activate virtual environment
3. Prepare a config file at `~/.aws/config` and copy paste the following contents. If you already have this file, you may just add the `[profile test]` section. Remember to replace the account id with the correct SSO id account value (e.g. the `clingen-aws-nonprod`'s id, all digits).

```
[default]
output = json
region = us-west-2

[profile test]
sso_start_url = https://clingen.awsapps.com/start
sso_region = us-west-2
sso_account_id = 123123123123 <-- find this in the SSO portal
sso_role_name = AWSAdministratorAccess

```

4. Run `python aws-refresh-credentials test`

```
$ python aws-refresh-credentials test
Getting credentials for profile test (arn:aws:iam::***:role/AWSAdministratorAccess)
Saved credentials for profile test
```

5. In case you get stuck in Step 4 and terminal stops at `Getting credentials for profile test (arn:aws:iam::123123123123:role/AWSAdministratorAccess)`, you can abort the process and run the following command instead: `docker run --rm -it -v ~/.aws:/root/.aws amazon/aws-cli sso --profile test login`. After you complete the browser verification process (you'll be instructed by the prompts in terminal), you can retry step 4.

6. Check out `~/.aws/credentials`, you should now see `test` profile credential is updated.
