# Links

The links property in general is used to specify resource relationships for resource objects. In our default implementation we include the “self” relationship which shows the link that this document is in response to and the mime type of the request:


    "self": {
        href: "http://joomla.dev/component/tada?view=todos&format=json&limit=2",
        type: "application/json; version=1.0"
    }



When appropriate, we also include “next” and “previous” pagination links in the section in the exact same form.

    "next":
    {
        href: "http://joomla.dev/component/tada?view=todos&format=json&limit=2&offset=2",
        type: "application/json; version=1.0"
    }

**Remember, this happens automatically! You do not have to do anything.**

You can define how your named entity will render with no problem at all. Just create a JSON view class and define a method with the form ‘_get’ + The name of your entity

States are directly related to request variables and those variables are used to limit results to a set or an individual record.
