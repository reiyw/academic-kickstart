+++
title = "Sending Notification to Slack from Jupyter Notebook"

date = 2017-06-06
lastmod = 2017-06-08
draft = false

tags = ["python", "jupyter", "slack"]
summary = "Implementation of a Slack notification when cell execution has finished."

[header]
image = ""
caption = ""
+++

If you want to run a cell that may take a long time, it would be helpful if [Jupyter](http://jupyter.org) sends a notification when the cell is finished. I implemented the feature by just combining Jupyter's [custom magic](http://ipython.readthedocs.io/en/stable/config/custommagics.html) and Slack's [Incoming Webhooks](https://api.slack.com/incoming-webhooks).

The following is my implementation. Put it in `~/.ipython/profile_default/startup/nbnotifier.py` to call from Jupyter.

```python
import json
import socket

from IPython.core.magic import register_line_magic
import requests


@register_line_magic
def slack(line):
    webhooks_url = '<Incoming Webhooks URL>'
    payload = {
        'text':
        line
        if line else 'A cell that was running on *{}* has finished.'.format(
            socket.gethostname())
    }
    r = requests.post(webhooks_url, data=json.dumps(payload))
```

Note that `'<Incoming Webhooks URL>'` should be replaced with yours.

The following shows usage. By writing `%slack [message]` at the end of the cell, you can receive notification when it is finished.

| Jupyter |   | Slack |
|---------|---|-------|
| ![nbnotifier_no-arg_jupyter](/img/nbnotifier_no-arg_jupyter.png) | > | ![nbnotifier_no-arg_slack](/img/nbnotifier_no-arg_slack.png) |
| ![nbnotifier_no-arg_jupyter](/img/nbnotifier_arg_jupyter.png) | > | ![nbnotifier_arg_slack](/img/nbnotifier_arg_slack.png) |
