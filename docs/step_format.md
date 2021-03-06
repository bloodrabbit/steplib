
# Step Repository Format

This description covers what's required for a Step repository
which can be submitted into this open StepLib collection.


## General requirements

A Step have to be a Git repository hosted on GitHub as a public repository.

**You can find an annotated Step repository** example in the `templates/step` folder
and a **full template Step repository** on GitHub: [https://github.com/steplib/step-template](https://github.com/steplib/step-template).

It have to contain:

* a step.sh file : this is the entry point of you Step
* a step.yml file : contains the description of the Step
* a LICENSE file (we prefer the MIT or the WTFPL license)

Recommended:

* a README.md file (readme file in Markdown format)


## Step format design goals

* the format should be as simple as possible, with the least amount of requirement
* the step repository should be small and fast to download
  * the Step Library should store all the step related resources as this repository won't be downloaded and processed as many times as the individual steps


## Step.yml and default values

The following properties are all required except if marked
as [optional] in the step.yml descriptor file.

**Workflow Notes:** All the attributes should be copied
to a workflow and the value of the properties is **not** expected
to change in the Workflow **except** if it's marked at the
property here in the list (with: @WorkflowUserMod) - in that case
it's expected that the user will be able to change the value of the property.

* name : Name / title of your step, presented in Step libraries, search results, UIs, ...
  * @WorkflowUserMod: the value of this property is expected to change in workflow; this property defines a default value for the Workflow; user modifiable in Workflow
  * [string] [required]
* description : detailed description of your Step in markdown format. You can include important notes and setup guide or anything important related to your step.
  * [string] [required] [format:markdown]
* website : website URL where others can read more about the step. Can be the step's github page or a dedicated website if you have one.
  * [string] [required]
* fork_url : a website URL where you have details about collaboration on the step, forking the repository etc. Can be the step's github page or a dedicated website page if you have one.
  * [string] [optinal|default:website's value]
* source : Step source code access information
  * [hash] [required]
* source/git : at the moment we only accept git repositories hosted on GitHub, as an open repository. This attribute should point to your Step's repository and should be an URL which can be passed to `git clone` directly.
  * [string] [required]
* host_os_tags : the Operating System tags which are supported by your step, in the following format: [short-os-code]-[the.os.version.number]. You should test your step on all the listed OS versions!
  * [array of strings] [required]
* project_type_tags : Project type tags, list all the project types this step can be used for. For example if the step is an iOS specific build step then include "ios" in the project type tags.
  * [array of strings] [optional|default:empty-array]
* type_tags : regular / generic tags, used for searching, filtering or grouping by StepLib programs/services.
  * [array of strings] [required]
* is_requires_admin_user : If your step doesn't require an administrator user account to run properly then you should set this to 'false'
  * [bool] [required]
* inputs : see the **Inputs** section for details
  * [array of hashes] [required]
* outputs : see the **Outputs** section for details
  * [array of hashes] [required]


### Inputs

Inputs are a list of Environment Variable Keys and user-friendly input related
descriptions / settings. A StepLib compatible program or service
should map the given input's value (the one the user specifies
for the given input, where the user generally see the *title* of the input)
to the specified environment key.

All the input values are expected to be text/string values and the step (in it's code) is
expected to validate the input and it's type if required.

* title : Title of the input (presentable to the user)
  * [string] [required]
* description : Optional longer description of the input.
  Can be a markdown formatted string, useful for guides,
  for example to include a step-by-step description on
  how to obtain a service specific token required for the input.
  * [string] [optional|default:""] [format:markdown]
* mapped_to : The environment variable which will contain the value
  the user provides. Don't include the dollar sign, just the environment
  variable's name.
  * [string] [required]
* is_expand : Whether to do an environment expansion
  on the value of the input before storing it in the environment.
  As an example you might want to include other environment variables in
  an email summarizing a build. If is_expand is true (default) and
  you (or the user) include let's say a `${XCODE_BUILDER_STATUS}` environment
  reference then the string ${XCODE_BUILDER_STATUS} will be replaced
  with the environment value of XCODE_BUILDER_STATUS.
  If is_expand is false then the string `${XCODE_BUILDER_STATUS}` will
  be included instead of the related environment value it references.
  * @WorkflowUserMod: the value of this property is expected to change in workflow; this property defines a default value for the Workflow; user modifiable in Workflow
  * [bool] [optional|default:true]
* is_required : whether it's a required input (true if the step can't
  properly work without specifying it)
  * [bool] [optional|default:false]
* value_options : specify this array of possible values
  if there's a pre-defined set of values the step can accept for this
  input. The values have to be strings as every input value is
  interpreted as string.
  * [array of strings] [optional|default:empty-array]
* value : the default value of the input.
  Useful to indicate the default value if your step has one for the input
  and for "step flavors" where you might want to fix an input value
  to achieve the flavor's purpose.
  * @WorkflowUserMod: the value of this property is expected to change in workflow; this property defines a default value for the Workflow; user modifiable in Workflow
  * [string] [optional|default:""]
* is_dont_change_value : if this is set to true then
  it indicates that the input's specified 'value' property should not be modified,
  should be kept as it is defined.
  Useful for "step flavors" - these inputs should be hidden
  from the user in GUI context.
  * [bool] [optional|default:false]


### Outputs

Similar to *inputs* the *outputs* is a list of environment variables.
The main difference is that outputs are not generated by or managed
by the StepLib programs / services, your step is responsible to generate these outputs,
and to make it accessible for other steps which will run after your
step finishes.

This list is used for StepLib programs / services to be able to
provide UI for connecting one step's outputs with other steps' inputs.

Title is meant to be presented on UIs, and the "mapped_to" parameter
identifies the Environment Variable Key your step will generate.

* title : Title of the output (presentable to the user)
  * [string] [required]
* description : Optional longer description of the output.
  Can be a markdown formatted string.
  * [string] [optional|default:""] [format:markdown]
* mapped_to : The environment variable which will contain
  the value of your step's output. Don't include the dollar sign, just the environment
  variable's name.
  * [string] [required]

> You can find an annotated Step repository example in the *templates/step* folder
> and a full template Step repository at [https://github.com/steplib/step-template](https://github.com/steplib/step-template).
