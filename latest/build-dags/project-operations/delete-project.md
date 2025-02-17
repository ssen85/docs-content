---
# metadata #
title:  Delete a Project
description: Learn how to delete a project.
date:
# taxonomy #
tags: ["projects"]
series:
seriesPart:
weight: 
---


## How to Delete a Project

{{<stack type="wizard">}}
{{% wizardRow id="Tool"%}}
{{% wizardButton option="Pachctl CLI" state="active" %}}
{{% wizardButton option="Console" %}}
{{% /wizardRow %}}

{{% wizardResults  %}}

{{% wizardResult val1="tool/pachctl-cli"%}}

   ```s
   pachctl delete project <project>
   ```

If the project you removed was set to your currently active context, make sure to assign a new one:

```s
pachctl config update context --project foo
```

{{% /wizardResult %}}

{{% wizardResult val1="tool/console"%}}
1. Open Console.
2. Navigate to **Projects** (the main page).
3. Scroll to the project you want to delete.
4. Select the ellipsis (...) icon and click **Delete Project**.

![delete a project](/images/projects/console-delete-project.gif)

{{% /wizardResult %}}

{{% /wizardResults  %}}

{{</stack>}}