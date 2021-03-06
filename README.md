# catbox-s3

Amazon S3 adapter for [catbox](https://github.com/hapijs/catbox).


### Options

- `bucket` - the S3 bucket. You need to have write access for it.
- `accessKeyId` - the Amazon access key.
- `secretAccessKey` - the Amazon secret access key.
- `region` - the Amazon S3 region. (Default: 'eu-west-1')


### Caching binary data

At the moment, Hapi doesn't support caching of non-JSONifiable responses (like Streams or Buffers, see [#1948](https://github.com/hapijs/hapi/issues/1948)).
If you want to use catbox-s3 for binary data, you have to handle it manually in your request handler:

```javascript
var Catbox = require('catbox');
var cache  = new Catbox.Client(require('catbox-s3'), {
    accessKeyId     : /* ... */,
    secretAccessKey : /* ... */,
    region          : /* ... */,
    bucket          : /* ... */
});

var handler = function (request, reply) {
    var cacheKey      = {
        id      : /* cache item id */,
        segment : /* cache segment name */
    };

    cache.get(cacheKey, function (err, result) {
        if (result) {
            return reply(result.item).type(/* response content type */);
        }

        yourBusinessLogic(function(err, data) {
            cache.set(cacheKey, data, /* expiration in ms */, function(err) {
                /* ... */
            });
            reply(result.item).type(/* response content type */);
        });
    });
};

```

### Running tests

In order to run the tests, set the aforementioned options as environment variables:

```shell
S3_ACCESS_KEY=<YOURKEY> S3_SECRET_KEY=<YOURSECRET> S3_BUCKET=<YOURBUCKET> npm test
```


### License

[MIT](LICENSE.txt)
