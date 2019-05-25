It's a good idea to setup a separate Exasol instance just for tests.

You may use free "Community Edition" running in virtual environment for this purpose. Please follow the [official guide](https://docs.exasol.com/get_started/tryexasol/communityedition.htm) for instructions.

Alternatively, you may use special version of [Exasol for Docker](https://github.com/exasol/docker-db). Please note that this version only works on Linux at this moment.

For testing purposes we assume that Exasol instance is running, responds to default connection string `localhost:8563`, username `SYS` and password `exasol`.

We use schema `EXASECRETS` in our examples. You may create it using following command:

{% highlight sql %}
CREATE SCHEMA EXASECRETS;
OPEN SCHEMA EXASECRETS;
{% endhighlight %}

Some examples may require multi-node setup, but it will be mentioned in the description.
