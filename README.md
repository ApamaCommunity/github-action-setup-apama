# GitHub Action setup-apama
This GitHub action installs Apama Community Edition on either Windows or Linux. 

The environment variables for the workflow are configured as if the `apama_env` script had been run/sourced. 
This means that subsequent steps can use Apama executables such as `engine_deploy` and `apamadoc`. 

If you wish to test on both Windows and Linux, you can use a Bash shell or PowerShell to execute Apama tools on both 
operating systems. Since the executable for running PySys tests has different extensions on Windows/Linux, this action 
sets an additional `APAMA_PYSYS` environment variable containing the script name so you can run it with 
`$APAMA_PYSYS run <args>` in your shell script. 

The action has only one input parameter, `apama-version`, which must be set to a valid 4-digit Apama version. 

It is recommended to use this action with a cache to avoid downloading and running the Apama installer every time 
the job executes. To do this, add the following after your checkout action and before setup-apama:

    env:
      # The 4-digit version to download from http://www.apamacommunity.com/downloads/
      SETUP_APAMA_VERSION: 10.5.3.2

    steps:
      - uses: actions/checkout@v2

      - name: Cached Apama installation
        # always-upload is a fork of actions/cache allows cache to be saved even if some later steps (e.g. testing) fail
        uses: pat-s/always-upload-cache@v2.1.3

        id: cache-apama-installer
        with:
            path: ~/cached-apama-installation.tar
            key: apama-${{ runner.os }}-${{ env.SETUP_APAMA_VERSION }}

      - uses: setup-apama@v1
        with:
          apama-version: ${{ env.SETUP_APAMA_VERSION }}

      - name: Run PySys tests
        shell: bash
        run: |
          echo APAMA_HOME is: $APAMA_HOME
          $APAMA_PYSYS run --mode=ALL --threads=auto --purge

Note that the Linux environment usually executes quite a lot faster than the Windows one, so if you don't need 
multi-platform testing, use a workflow that runs only on Linux for maximum efficiency. 

See https://github.com/ApamaCommunity for a complete sample workflow using this action. 

# License

Copyright (C) 2020 Software AG, Darmstadt, Germany and/or its licensors

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this file except in compliance with the License. You may obtain a copy of the License at
  
		http://www.apache.org/licenses/LICENSE-2.0
  
Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the License for the specific language governing permissions and limitations under the License.

Use of this action implies that you have also agreed to the terms for using Apama Community Edition which you can read at http://www.apamacommunity.com/terms-conditions/
