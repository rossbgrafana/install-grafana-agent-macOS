# Installing the Grafana Agent on MacOS via Binary

## Lab Summary

This lab is designed to walk the user through the process of setting up the Grafana Agent on a macOS system, adding the macOS integration into a free Grafana Cloud account, and then configuring and testing the sending of metrics from the system to Grafana Cloud.  

When this lab has been completed, you'll have:
 - Installed the Grafana Agent in static mode
 - Configured the Grafana Agent via the config.yml file
 - Setup the macOS integration on your Grafana Cloud stack
 - Made integration configuration selections
 - Tested the connection between the system and Grafana Cloud
 - Seen how to view the pre-configured dashboards, alerts, and recording rules

# Lab Steps

## Installing the Grafana Agent static mode


  **Note:**  To install the static agent on MacOS, you'll need to have Homebrew installed.

1.  To install Homebrew, please go to the Homebrew installation page and <a href="https://brew.sh/">follow the instructions there.</a>

  **Note:**  Alternatively, if you've already got Homebrew installed, please continue to the next step.

2. Open the Terminal App by pressing `Cmd+Spacebar` and typing `Terminal`, then press `ENTER`

3. Update Homebrew with the command:

    $ `brew upgrade`

4. Install the Grafana Agent with the command:

    $ `brew install grafana-agent`

  **Note:**  If the agent is not installed, this command will install the agent, which might take a minute or two.

6. If the agent is already installed, but not up to date, the brew  install command will notify you of that and update the agent.

  **Note:**  After the agent is installed, brew will notify you that the agent uses a `config.yml` file and will display it's location, as well as some other helpful commands.

## Configuring Grafana Agent static mode

1.  To create a blank `config.yml` file that you can then edit to configure the Grafana Agent, use the command:

    $ `touch $(brew --prefix)/etc/grafana-agent/config.yml`

2.  Verify your agent config file has contents with the command:

    $ `cat $(brew --prefix)/etc/grafana-agent/config.yml`

    There should be YAML data in this file.

## Setting up the macOS integration on your Grafana Cloud stack

1.  Sign in to your Grafana Cloud account from www.grafana.com

2.  Click on the **My Account** link at the top right of the interface to go to your the Grafana Cloud Portal Overview for your account.

3.  Check the drop-down at the top left and make sure that you are in the Organization for which you want to enable the macOS integration for Grafana Cloud.

4.  Find the **Manage your Grafana Cloud Stack** text about 1/2 down the page, then find the **Grafana** grey box and click on the **Launch** button to launch your Grafana instance.

![Launch button for your Grafana stack](./images/macos-graf-launch.png)

5.  When the **Welcome to Grafana Cloud** page loads, go to the top left of the interface and click on the 3-line **Topic Menu** located below the Grafana logo and to the left of the **Home** text.

![Launch button for your Grafana stack](./images/macos-home-conn.png)

6.  Click on the **Connections** menu item and then either scroll to find the **macOS** selection, or type `macOS` into the **Search Connections** dialog near the top of the interface.

7.  Once the **macOS** selection shows up, click on that and then follow the instructions to install the **macOS** integration.

## Making Configuration Selections

1.  Under Section **2. Make configuration selections**, choose the correct **Architecture** and **Installation method** to match what you have, where `Amd64` is for Intel-type systems and `Arm64` is used for the Apple Silicon-type systems.

2.  In Section **3. Prepare your agent configuration file**, check the two requirements are met.

3.  Next navigate to the **Value for Hostname** dialog box slightly lower on the page and enter the hostname you want to use to identify the host being observed.

    Example:  `myhostname`

  **Note:** This step will automatically change the `your-instance-name` text in the two code examples to match your hostname, so you don't have to take an extra step to replace that text!

4.  Next open the `config.yml` file on your local system with your favorite editor and then find the `integrations` section.

  **Note:**  On a system where the Grafana Agent was installed via Homebrew, the `config.yml` file is usually found at the following location:

`$(brew --prefix)/etc/grafana-agent/config.yml`

Example command to edit this file:

$ `sudo vim $(brew --prefix)/etc/grafana-agent/config.yml`

5.  Copy the example lines from the web page using the **Copy to clipboard** selection at the bottom of the example code block, then insert that into the `config.yml`` file under the `integrations` section.

6.  You'll need to confirm the `your-instance-name` text was replaced with your information (myhostname), such as:

```  node_exporter:
    enabled: true
    relabel_configs:
    - replacement: 'myhostname'
      target_label: instance
    - replacement: "integrations/macos-node"
      target_label: job
    metric_relabel_configs:
    - action: keep
      regex:
    ...
```

7.  Leaving the file open in the editor, now copy to the clipboard the code example under the **Logs** section header.  You'll need to confirm the `your-instance-name` text was again replaced with your information, (myhostname) such as:

```    - job_name: integrations/node_exporter_direct_scrape
      static_configs:
      - targets:
        - localhost
        labels:
          __path__: /var/log/*.log
          instance: 'myhostname>'
          job: integrations/macos-node
      pipeline_stages:
      - multiline:
          firstline: '^([\w]{3} )?[\w]{3} +[\d]+ [\d]+:[\d]+:[\d]+|[\w]{4}-[\w]{2}-[\w]{2} [\w]{2}:[\w]{2}:[\w]{2}(?:[+-][\w]{2})?'
 ...
```

8.  Now find the section that is named `logs.configs.scrape_configs` and paste the log configuration into the configuration file.

9.  You can save and exit the configuration file now.
   
10.  Restart the Grafana Agent on your local system with the command:

    $ `brew services restart grafana-agent`

## Test the system to Grafana integration connection

1.  Now test your connection by navigating down to the **Test connection** section and click the blue **Test connection** button, which may take a minute to return results.

2.  If your system is properly configured, you should shortly see the text:

    ```
    The agent is now collecting data from your machine.
    ```

3.  Next, navigate to the **Install dashboards and alerts** and click on the **Install** blue button to install the pre-configured dashboards and alerts.

## View the pre-configured dashboards and alerts and recording rules

1.  Lastly, you can now click on the **View Dashboards** button to see a list of the pre-configured dashboards you just installed and start exploring your system's metrics.

You'll see something similar to this:

![List of macOS integration dashboards](./images/macos-int-dashboards.png)

2.  Additionally, you can click on the **View alerts and recording rules** button to access those features.

You'll see something similar to this:

![List of macOS integration rules](./images/macos-int-alerts.png)
