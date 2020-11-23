# github-action-setup-apama
This GitHub action installs Apama Community Edition on either Windows or Linux. 

The environment variables are configured as if the `apama_env` script had been run/sourced. 

This means subsequent steps can use Apama executables such as `engine_deploy` and `apamadoc`. 
The bin directories from the JDK and Python installation included with Apama are also added to PATH. 

If you wish to test on both Windows and Linux, you can use a bash shell to execute Apama tools on both operating systems. 
Since the executable for running PySys tests has different extensions on Windows/Linux, this action sets an additional 
`APAMA_PYSYS` environment variable containing the script name so you can add `$APAMA_PYSYS run <params here>` to your 
shell script. 

It is recommended to use this action with a cache to avoid downloading and running the Apama installer every time 
the job executes. To do this, add the following after your checkout action and before setup-apama:

      - name: Cached Apama installation
        # always-upload is a fork of actions/cache allows cache to be saved even if some later steps (e.g. testing) fail
        uses: pat-s/always-upload-cache@v2.1.3
        with:
            # TODO: keep this version matching the Apama version you wish to use
            key: apama-${{ runner.os }}-10.5.3.2
            path: ~/cached-apama-installation.tar
