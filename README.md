# apigeetool

This is a tool for deploying API proxies and Node.js applications to the Apigee Edge platform. The tool also lets you list and undeploy API proxies.

* [Installation](#installation)
* [What you need to know about apigeetool](#whatyouneed)
* [Command reference and examples](#reference)
* [Original tool](#original)

# <a name="installation"></a>Installation

`apigeetool` is a Node.js module and you can install it using npm:

`npm install -g apigeetool`

*NOTE*: The `-g` option places the apigeetool command in your PATH. On "\*nix"-based machines, `sudo` may be required with the `-g` option. If you do not use `-g`, then you need to add the apigeetool command to your PATH manually. Typically, the `-g` option places modules in: `/usr/local/lib/node_modules/apigee-127` on *nix-based machines.

# <a name="whatyouneed"></a>What you need to know about apigeetool

You must have an account on Apigee Edge to perform any `apigeetool` functions. These functions include:

* deploying an API proxy to Edge,
* undeploying an API proxy from Edge,
* deploying Node.js apps to Edge,
* listing deployed API proxies on Edge,
* retrieving deployed proxies and apps from Edge,
* deleting proxy definitions from Edge, and
* retreiving log messages from Node.js apps deployed to Edge.

You need to be familiar with basic concepts and features of Apigee Edge such as API proxies, organizations, and environments.

For more information, refer to the [Apigee Edge docs](http://apigee.com/docs/).

# <a name="commonargs"></a>Common Parameters

The following parameters are available on all of the commands supported by
this tool:

`--username  -u`  
(required) Your Apigee account username.

`--password  -p`  
(required) Your Apigee account password.

`--organization  -o`  
(required) The name of the organization to deploy to.

`--help  -h`  
(optional) Displays help on this command.

`--baseuri   -L`  
(optional) The base URI for you organization on Apigee Edge. The default is the base URI for Apigee cloud deployments is `api.enterprise.apigee.com`. For on-premise deployments, the base URL may be different.

`--debug -D`  
(optional) Prints additional information about the deployment, including router and message processor IDs.

`--verbose   -V`  
(optional) Prints additional information as the deployment proceeds.

`--json  -j`  
(optional) Formats the command's response as a JSON object.

`--cafile -c`
(optional) The names of one of more PEM files that represent trusted certificate authorities.
Multiple file names may be comma-separated. Use this to communicate with an installation
of Apigee Edge that uses a custom certificate for API calls.

`--insecure -k`
(optional) Do not validate the TLS certificate of the HTTPS target for API calls.
Use this to communicate with an installation of Apigee Edge that does not use a
trusted TLS certificate.

# <a name="reference"></a>Command reference and examples

* [deploynodeapp](#deploynodeapp)
* [deployproxy](#deployproxy)
* [undeploy](#undeploy)
* [listdeployments](#listdeployments)
* [fetchproxy](#fetchproxy)
* [getlogs](#getlogs)
* [delete](#delete)

## <a name="deploynodeapp"></a>deploynodeapp

Deploys a Node.js app to Apigee Edge as an API proxy. With your Node.js app deployed to Edge, you can take advantage of Edge features like security, quotas, caching, analytics, trace tool, and more.

#### Examples

Deploys a Node.js app to Apigee Edge.

    apigeetool deploynodeapp -u sdoe@apigee.com -o sdoe -e test -n 'Test Node App 2' -d . -m app.js -b /node2

Deploys a Node.js app to both the default (HTTP) and secure (HTTPS) virtual hosts.

    apigeetool deploynodeapp -u sdoe@apigee.com -o sdoe -e test -n 'Test Node App 2' -d . -m app.js -b /node2 -v default,secure

#### Required parameters

The following parameters are required. However, if any are left unspecified
on the command line, and if apigeetool is running in an interactive shell,
then apigeetool will prompt for them.

For example, if you do not specify a password using "-p", apigeetool will
prompt for the password on the command line.

See [Common Parameters](#commonargs) for a list of additional parameters, including
the "-u" and "-p" parameters for username and password, and the "-o" parameter
for organization name, all of which are required.

`--api   -n`  
(required) The name of the API proxy. The name of the API proxy must be unique within an organization. The characters you are allowed to use in the name are restricted to the following: `A-Z0-9._\-$ %`.

`--environment   -e`  
(required) The name of the environment to deploy to.  

`--directory -d`  
(required) The path to the root directory of the API proxy on your local system.

`--main  -m`  
(required) The Node.js file you want to be the main file.

#### Optional parameters

`--virtualhosts  -v`  
(optional) A comma-separated list of virtual hosts that the deployed app will use. The two most common options are `default` and `secure`. The `default` option is always HTTP and `secure` is always HTTPS. By default, `apigeetool deploynodeapp` uses only the `default` virtual host. Note that on the Apigee Edge cloud platform, all new proxies are assigned two virtual hosts: `default` and `secure`. If you want your deployed Node.js app to use both HTTP and HTTPS, specify `-v default,secure`.

`--base-path -b`  
(optional) The base path of the API proxy. For example, for this API proxy, the base path is `/example-proxy`: `http://myorg-test.apigee.net/example-proxy/resource1`.

`--import-only   -i`  
(optional) Imports the API proxy to Apigee Edge but does not deploy it.

`--resolve-modules   -R`  
(optional) If the API proxy includes Node.js modules (e.g., in a `node_modules` directory), this option updates them on Apigee Edge without uploading them from your system. Basically, it's like running "npm install" on Apigee Edge in the root directory of the API proxy bundle.  

`--upload-modules    -U`  
(optional) If specified, uploads Node.js modules from your system to Apigee Edge rather than resolving the modules directly on Apigee Edge. This is the default.

## <a name="deployproxy"></a>deployproxy

Deploys an API proxy to Apigee Edge. If the proxy is currently deployed, it will be undeployed first, and the newly deployed proxy's revision number is incremented.

#### Example

Deploys an API proxy called example-proxy to Apigee Edge. Per the `-d` flag, the command is executed in the root directory of the proxy bundle.

    apigeetool deployproxy  -u sdoe@example.com -o sdoe  -e test -n example-proxy -d .

#### Required parameters

The following parameters are required. However, if any are left unspecified
on the command line, and if apigeetool is running in an interactive shell,
then apigeetool will prompt for them.

See [Common Parameters](#commonargs) for a list of additional parameters, including
the "-u" and "-p" parameters for username and password, and the "-o" parameter
for organization name, all of which are required.

`--api   -n`  
(required) The name of the API proxy. Note: The name of the API proxy must be unique within an organization. The characters you are allowed to use in the name are restricted to the following: `A-Z0-9._\-$ %`.

`--environment   -e`  
(required) The name of the environment to deploy to.  

`--directory -d`  
(required) The path to the root directory of the API proxy on your local system.

#### Optional parameters

`--base-path -b`  
(optional) The base path of the API proxy. For example, for this API proxy, the base path is /example-proxy: http://myorg-test.apigee.net/example-proxy/resource1.

`--import-only   -i`  
(optional) Imports the API proxy to Apigee Edge but does not deploy it.

`--resolve-modules   -R`  
(optional) If the API proxy includes Node.js modules (e.g., in a `node_modules` directory), this option updates them on Apigee Edge without uploading them from your system. Basically, it's like running npm on Apigee Edge in the root directory of the API proxy bundle.  

`--upload-modules    -U`  
(optional) If specified, uploads Node.js modules from your system to Apigee Edge.

## <a name="undeploy"></a>undeploy

Undeploys a named API proxy or Node.js app deployed on Apigee Edge.

#### Example

Undeploy the proxy named "example-proxy".

    apigeetool undeploy -u sdoe@example.com -o sdoe  -n example-proxy -e test -D

#### Required parameters

The following parameters are required. However, if any are left unspecified
on the command line, and if apigeetool is running in an interactive shell,
then apigeetool will prompt for them.

See [Common Parameters](#commonargs) for a list of additional parameters, including
the "-u" and "-p" parameters for username and password, and the "-o" parameter
for organization name, all of which are required.

`--api   -n`  
(required) The name of the API proxy or app to undeploy.

`--environment   -e`  
(required) The environment on Apigee Edge where the API proxy or app is currently deployed.

#### Optional parameters

`--revision  -r`  
(optional) Specify the revision number of the API proxy to undeploy.

## <a name="listdeployments"></a>listdeployments

Lists the API proxies deployed on Apigee Edge for the specified organization. Lets you filter the result by API proxy name or environment.

#### Examples

List all API proxies in an organization:

    $ apigeetool listdeployments -u sdoe@example.com -o sdoe -e test`

List API proxies named "example-proxy":

    $ apigeetool listdeployments -u sdoe@example.com -o sdoe -n example-proxy

#### Required parameters

See [Common Parameters](#commonargs) for a list of additional parameters, including
the "-u" and "-p" parameters for username and password, and the "-o" parameter
for organization name, all of which are required.

#### Required, mutually exclusive parameters

`--api   -n`
(You must specify either `api` or `environment` in this command) The name of the API proxy to list.

`--environment   -e`  
(You must specify either `api` or `environment` in this command) The environment for which you want to list deployments. When `-e` is specified, the command lists all deployed proxies in the environment.

#### Optional parameters

`--long  -l`
(optional) Returns additional information about the API proxy or Node.js app,
including the URL to use as the base path for each one.

`--revision  -r`
(optional) Filters the list by the specified revision number.

## <a name="fetchproxy"></a>fetchproxy

Fetches a deployed API proxy or Node.js application from Apigee Edge. The
result will be a ZIP file that contains the contents of the entire
proxy.

Regardless of whether "deployproxy" or "deploynodeapp" is used to deploy the
proxy or app, the result of "fetchproxy" will always be a ZIP file that
represents an API proxy. The resulting proxy may be "unzipped" and
re-deployed using "deployproxy."

#### Example

Fetch the deployed proxy named "example-proxy".

    apigeetool fetchproxy -u sdoe@example.com -o sdoe -n example-proxy -r 1

#### Required parameters

The following parameters are required. However, if any are left unspecified
on the command line, and if apigeetool is running in an interactive shell,
then apigeetool will prompt for them.

See [Common Parameters](#commonargs) for a list of additional parameters, including
the "-u" and "-p" parameters for username and password, and the "-o" parameter
for organization name, all of which are required.

`--api  -n`  
(required) The name of the API proxy or app to undeploy.

`--revision  -r`
(optional) Specifies the revision to retrieve.

#### Optional parameters

`--file -f`
(optional) The name of the file to write as the result. If not specified,
then the file name will be the same as the name passed to the "name"
parameter.

## <a name="delete"></a>delete

Delete all revisions of a proxy from Apigee Edge.

It is an error to delete a proxy that still has deployed revisions. Revisions
must be undeployed using "undeploy" before this command may be used.

#### Example

Delete the proxy named "example-proxy".

    apigeetool delete -u sdoe@example.com -o sdoe -n example-proxy

#### Required parameters

The following parameters are required. However, if any are left unspecified
on the command line, and if apigeetool is running in an interactive shell,
then apigeetool will prompt for them.

See [Common Parameters](#commonargs) for a list of additional parameters, including
the "-u" and "-p" parameters for username and password, and the "-o" parameter
for organization name, all of which are required.

`--api  -n`  
(required) The name of the API proxy or app to undeploy.

## <a name="getlogs"></a>getlogs

Retrieve the last set of log records from a Node.js application deployed to Apigee Edge.

The resulting log files will be written directly to standard output. Each is prefixed with:

* A timestamp, in UTC by default
* An indication of whether the log record came from standard output or standard error
* A unique identifier of the server where the log was generated.

Since Apigee Edge, by default, deploys each Node.js application on at least two
different servers, most applications will see at least two sets of logs. These are
sorted in time stamp order.

The log records from this command come from a cache inside Apigee Edge that retains
the last 500 lines of log output from each server. The cache is a circular buffer,
so older messages will be discarded when it fills up.

By default, the last set of log records will be pulled and written to standard output. However, if the
"-f" option is used, then "apigeetool" will poll Edge for new log records and append them to standard
output, in the manner of "tail -f". (By default, the tool polls every
five seconds).

For example, a set of log records might look like this:

<pre>
[2014-11-05T01:30:01.530Z nodejs/stderr svr.362] *** Starting script
[2014-11-05T01:30:09.182Z nodejs/stderr svr.362] 2014-11-05T01:30:09.181Z - debug: 1/4. this is a debug log
[2014-11-05T01:30:09.186Z nodejs/stdout svr.362] 2014-11-05T01:30:09.186Z - info: 2/4. this is an info log
[2014-11-05T01:30:09.187Z nodejs/stdout svr.362] 2014-11-05T01:30:09.187Z - warn: 3/4. this is a warning log
[2014-11-05T01:30:09.188Z nodejs/stderr svr.362] 2014-11-05T01:30:09.188Z - error: 4/4. this is an error log
[2014-11-05T01:48:21.419Z nodejs/stderr svr.828] *** Starting script
[2014-11-05T01:48:28.637Z nodejs/stderr svr.828] js-bson: Failed to load c++ bson extension, using pure JS version
[2014-11-05T01:48:29.801Z nodejs/stderr svr.828] 2014-11-05T01:48:29.800Z - debug: 1/4. this is a debug log
[2014-11-05T01:48:29.804Z nodejs/stdout svr.828] 2014-11-05T01:48:29.804Z - info: 2/4. this is an info log
[2014-11-05T01:48:29.805Z nodejs/stdout svr.828] 2014-11-05T01:48:29.805Z - warn: 3/4. this is a warning log
[2014-11-05T01:48:29.806Z nodejs/stderr svr.828] 2014-11-05T01:48:29.806Z - error: 4/4. this is an error log
</pre>

#### Required Parameters

The following parameters are required. However, if any are left unspecified
on the command line, and if apigeetool is running in an interactive shell,
then apigeetool will prompt for them.

See [Common Parameters](#commonargs) for a list of additional parameters, including
the "-u" and "-p" parameters for username and password, and the "-o" parameter
for organization name, all of which are required.

`--api  -n`  
(required) The name of the deployed app to get logs from.

`--environment   -e`  
(required) The environment on Apigee Edge where the app is currently deployed.

#### Optional Parameters

`--streaming  -f`
(optional) If specified, do not exit, but instead poll Apigee Edge for new log
records and write them to standard output in the manner of "tail -f."

`--timezone  -z`
(optional) If specified, use the time zone to format the timestamps on the
log records. If not specified, then the default is UTC. The timestamp name
should be a name such as "PST."

# <a name="original"></a>Original Tool

This module replaces the original "apigeetool," which was written in Python.
It is also called "apigeetool" and resides here:

https://github.com/apigee/api-platform-tools
