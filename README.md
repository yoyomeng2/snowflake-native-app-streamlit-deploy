# Snowflake Native App Streamlit Deploy - Guided Lab

This Hands-on Lab guides you through deploying a Streamlit application as a Snowflake Native Application using the Snowflake CLI.
It builds on the previous [Streamlit Deploy](https://github.com/yoyomeng2/streamlit-deploy).

You will fork and start from a minimal `start` branch and build the app step-by-step, using tools such as `uv`, the Snowflake CLI, and GitHub Actions for automated deployment and testing.

The repository is intentionally structured to let you create the necessary files and configurations yourself.
The `main` branch contains the full reference implementation for comparison.

> [!NOTE]
> This pattern is preferable to the previous [Streamlit Deploy](https://github.com/yoyomeng2/streamlit-deploy) because it support a workspace SDLC pattern and versions of the application that otherwise need to be manually account for or disregarded.
> As such, this pattern is suitable for production deployments beyond POCs or adhoc analysis.

## Getting Started

First, for this repository to your own GitHub account so that you can configure Secrets and trigger Actions.

Click the "Fork" at the top of this page, then clone your fork:

```bash
git clone https://github.com/YOUR-USERNAME/snowflake-native-app-streamlit-deploy.git
```

Then open the repository in your terminal or IDE of choice.

## Requirements and Setup

- uv [installation instructions](https://docs.astral.sh/uv/getting-started/installation/)
- Python 3.8 or later (see below for installation instructions)
- Snowflake CLI [installation instructions](https://docs.snowflake.com/en/developer-guide/snowflake-cli/installation/installation)

If you don't have Python installed, follow the [uv Installing Python instructions](https://docs.astral.sh/uv/guides/install-python/) or run the following command to install the latest version of Python:

```bash
uv python install
```

Once you have Python installed, use `uv` to create a virtual environment and install the required packages:

```bash
uv sync
```

## Streamlit Deploy Lab

Things get a bit more interesting here.
No longer are we just deploying the application; now we’ll need to generate a [Snowflake - Create an application package](https://docs.snowflake.com/en/developer-guide/native-apps/creating-app-package#about-application-packages).

The Snowflake CLI will help with this too, so first let's get a Snowflake Native App Streamlit project set up.

> [!NOTE]
> Many of these steps are possible with Snowflake SQL but teh CLI minimized that effort by avoiding manual steps like creating as stage and the application.

You can peruse the repo for the final setup, but we'l walk through the steps to get there.

### Checkout the Start Branch

Before we start, let's checkout the `start` branch, which contains minimal files leaving the work for you to complete:

```bash
git checkout start
```

If you get stuck along the way, you can always refer to the `main` branch, which contains the completed code.

Your repository should only contain a few files at the root including this README.

## Create a Native Application

The easiest way to start is by following the [Snowflake - Creating a Snowflake Native App project](https://docs.snowflake.com/en/developer-guide/snowflake-cli/native-apps/initiate-app) docs.
Since we want to stick with Streamlit for our Native App, we’ll use that [project template](https://github.com/snowflakedb/snowflake-cli-templates/tree/main/app_streamlit_python) to start:

```bash
snow init nativeapp --template app_streamlit_python -D query_warehouse=mywarehouse -D stage=stage
```

Hit enter to accept any default values. It should create a directory structure like this:

```bash
 snowflake-native-app-streamlit-deploy git:(main) ✗ tree nativeapp
nativeapp
├── app
│   ├── manifest.yml
│   ├── README.md
│   └── setup_script.sql
├── local_test_env.yml
├── pytest.ini
├── README.md
├── scripts
│   ├── any-provider-setup.sql
│   └── shared-content.sql
├── snowflake.yml
└── src
    ├── module-add
    │   └── src
    │       ├── main
    │       │   └── python
    │       │       └── add.py
    │       └── test
    │           └── python
    │               └── test_add.py
    └── module-ui
        ├── src
        │   ├── environment.yml
        │   └── ui.py
        └── test
            └── test_ui.py

13 directories, 14 files
```

Notice the difference within the `naviteapp` directory from what `init --template example_streamlit` produced and this:

- An `app/` directory with manifest
- A `src/` directory with code
- A `snowflake.yml` with a different schema

The generated repository includes a README to follow and a Conda environment config called `local_test_env.yml`, however, we will favor using `uv` over Conda.

> [!NOTE]
> Conda may be more appropriate for your use case, especially if you need to manage complex dependencies or specific versions of libraries.
> However, for simplicity and as a general best practice we will use a Python virtual environment created via `uv`.

At this piont, you should be able to run the application locally:

```bash
uv run streamlit run nativeapp/src/module-ui/src/ui.py
```

You should see a Streamlit app running at `http://localhost:8501` and there will be an error about a UDF.

> [!NOTE]
> `uv run commands are used to run Python scripts in the virtual environment created by `uv   sync`.
> This ensures that the correct dependencies are used.

## Deploying to Snowflake Manually

The command to do this is slightly different now that it’s a Native App that runs Streamlit, not just a bare Streamlit app.

> [!IMPORTANT]
> The folliwing `snow` commands assume you have the Snowflake CLI installed and configured with a connection to your Snowflake account.
> If you have issues, check your `~/.snowflake/config.toml` file to ensure you have a connection setup.

This command will create a new application package in Snowflake and deploy the application:

```bash
snow app run --project nativeapp
```

> [!TIP]
> Note the URL and navigate to it in your browser.
> You should see the same Streamlit app running as you did locally, but now hosted in Snowflake.
> Additionally, the error is gone because the UDF was deployed as well.

> [!TIP]
> Also notice the application created includes your user name.
> This is important because it allows you to have multiple versions of the application running at the same time, which is useful for testing, development, and release management.

After verifying, you're ready to setup GitHub Actions for deployment. First, tear down the app:

```bash
snow app teardown --project nativeapp
```

## Deploying with GitHub Actions

Now that we’ve validated our app locally, manually deployed to Snowflake, and tested, it’s time to automate deploying the Streamlit app to Snowflake with Actions. As we saw earlier, the Snowflake CLI makes this simple with `snow app run`.

Assuming your key auth from the prior section is still valid you should just have to make a minor tweak to the previous workflow file. If you do not have key authentication setup, follow the steps in the [Key Pair Authentication Setup](https://github.com/yoyomeng2/streamlit-deploy?tab=readme-ov-file#key-pair-authentication-setup) section of the Streamlit Deploy lab.

The GitHub Actions workflow in this project is a bit more robust than the previous Streamlit Deploy lab.
Rather than have you create it all, let’s take a look at the setup:

```bash
➜  snowflake-native-app-streamlit-deploy git:(main) ✗ tree .github
.github
├── actions
│   ├── common-setup
│   │   └── action.yml         # This action sets up the environment for checks
│   └── create-version
│       └── action.yml         # This action creates a version of the native app package
├── copilot-instructions.md    # A suggested file for Copilot users with helpful prompts
├── dependabot.yml             # A suggested Dependabot config file for Python dependencies
└── workflows
    ├── checks.yml             # This workflow calls the common-setup action and runs tests and checks
    └── deploy-to-sandbox.yml  # This workflow calls the checks workflow and then the create-version action on push to feature/ or fix/ branches
```

The final command in the `create-version` Action file will command will create an application package that you can see in Snowsight > Project > App Packages called “MY_STREAMLIT_PYTHON_APP_PKG_RUNNER” where “RUNNER” (aka. the GitHub Actions user) is the user name that created it.

After you push your changes, you should see the Actions run in GitHub and the application package created in Snowflake.

## A Complete Example

For a complete example of a Snowflake Native App Streamlit deployment, see the [snowflake-share-back](https://bitbucket.org/phdata/snowflake-share-back/src/main/) repository in the phData Bitbucket.

It contains a fully functional Streamlit application that is deployed as a Snowflake Native App, including the necessary files and configurations to run the app in Snowflake. There are some differences in the pyproject structure, but the core concepts are the same.

## Next Steps

Congratulations! You have successfully deployed a Native App to Snowflake app using GitHub Actions.

Proceed to the XXX repo for a tutorial on TBD.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
