# requests代码阅读

README

为啥要写这库，别人不好要自己写，巴拉巴拉。

例子

```
>>> r = requests.get('https://api.github.com', auth=('user', 'pass'))
>>> r.status_code
204
>>> r.headers['content-type']
'application/json'
>>> r.content

```

这个库能干嘛：发送HEAD,GET,POST,PUT,PATCH,DELETE请求。可以从数据，文件，字典参数中添加头。

特性：

- SSL验证
- HEAD,GET,POST,PUT,PATCH,DELETE请求
- 异步请求
- cookie会话
- 身份验证
- 自动化编码
- 对request/response提供字典接口
- 多文件载入
- Unicode，gzip和deflate响应的自动编码。
- 完全支持unicode URL和域名。



## 从单元测试开始

```python
PORT = os.environ.get('HTTPBIN_PORT', '7077')

HTTPBIN_URL = 'http://0.0.0.0:%s/' % (PORT)
# HTTPBIN_URL = 'http://127.0.0.1:8000/'


def httpbin(*suffix):
    """Returns url for HTTPBIN resource."""

    return HTTPBIN_URL + '/'.join(suffix)


SERVICES = (httpbin, )
```



httpin是用来拼接请求路径。看到的指南上是，httpin返回的一个字典的模板，我们可以将http请求参数写入其中。

第一个测试模块

```python
def test_HTTP_200_OK_GET(self):
    r = requests.get(httpbin('/get'))
    self.assertEqual(r.status_code, 200)
```

直接使用get方法。get方法怎么定义的？

```python
def get(url, **kwargs):
    """Sends a GET request. Returns :class:`Response` object.

    :param url: URL for the new :class:`Request` object.
    :param **kwargs: Optional arguments that ``request`` takes.
    """

    kwargs.setdefault('allow_redirects', True)
    return request('get', url, **kwargs)
```

多个参数但是这里只用了一个url，直接就是用request去请求了。

```python
def request(method, url, **kwargs):
    """Constructs and sends a :class:`Request <Request>`.
    Returns :class:`Response <Response>` object.

    :param method: method for the new :class:`Request` object.
    :param url: URL for the new :class:`Request` object.
    :param params: (optional) Dictionary or bytes to be sent in the query string for the :class:`Request`.
    :param data: (optional) Dictionary or bytes to send in the body of the :class:`Request`.
    :param headers: (optional) Dictionary of HTTP Headers to send with the :class:`Request`.
    :param cookies: (optional) Dict or CookieJar object to send with the :class:`Request`.
    :param files: (optional) Dictionary of 'name': file-like-objects (or {'name': ('filename', fileobj)}) for multipart encoding upload.
    :param auth: (optional) Auth tuple to enable Basic/Digest/Custom HTTP Auth.
    :param timeout: (optional) Float describing the timeout of the request.
    :param allow_redirects: (optional) Boolean. Set to True if POST/PUT/DELETE redirect following is allowed.
    :param proxies: (optional) Dictionary mapping protocol to the URL of the proxy.
    :param return_response: (optional) If False, an un-sent Request object will returned.
    :param session: (optional) A :class:`Session` object to be used for the request.
    :param config: (optional) A configuration dictionary.
    :param verify: (optional) if ``True``, the SSL cert will be verified. A CA_BUNDLE path can also be provided.
    """

    s = kwargs.get('session') or sessions.session()
    return s.request(method=method, url=url, **kwargs)
```

request参数比较多。它说的是构建并发送一个request对象，返回一个response对象。

大胆猜测一下，这个应该是拿到url并进行访问，然后返回获取到的数据。

再看看这个session.session()函数，

```python
def session(**kwargs):
    """Returns a :class:`Session` for context-management."""

    return Session(**kwargs)
```

很简单，只返回了一个Session对象。再大胆猜测一下，正常情况下，kwargs是要带一些值的，比如一些信息，或者用户的账户密码，如上面request所示。

```python
class Session(object):
    """A Requests session."""

    __attrs__ = [
        'headers', 'cookies', 'auth', 'timeout', 'proxies', 'hooks',
        'params', 'config']


    def __init__(self,
        headers=None,
        cookies=None,
        auth=None,
        timeout=None,
        proxies=None,
        hooks=None,
        params=None,
        config=None,
        verify=None):

        self.headers = headers or {}
        self.cookies = cookies or {}
        self.auth = auth
        self.timeout = timeout
        self.proxies = proxies or {}
        self.hooks = hooks or {}
        self.params = params or {}
        self.config = config or {}
        self.verify = verify

        for (k, v) in defaults.items():
            self.config.setdefault(k, v)

        self.poolmanager = PoolManager(
            num_pools=self.config.get('pool_connections'),
            maxsize=self.config.get('pool_maxsize')
        )

        # Set up a CookieJar to be used by default
        self.cookies = {}

        # Add passed cookies in.
        if cookies is not None:
            self.cookies.update(cookies)
```

这个类的定义非常长，这里只放构造函数在这里。

那就从这个类中看看之前s.request(method=method, url=url, **kwargs)的定义

```python
    def request(self, method, url,
        params=None,
        data=None,
        headers=None,
        cookies=None,
        files=None,
        auth=None,
        timeout=None,
        allow_redirects=False,
        proxies=None,
        hooks=None,
        return_response=True,
        config=None,
        prefetch=False,
        verify=None):

        """Constructs and sends a :class:`Request <Request>`.
        Returns :class:`Response <Response>` object.

        :param method: method for the new :class:`Request` object.
        :param url: URL for the new :class:`Request` object.
        :param params: (optional) Dictionary or bytes to be sent in the query string for the :class:`Request`.
        :param data: (optional) Dictionary or bytes to send in the body of the :class:`Request`.
        :param headers: (optional) Dictionary of HTTP Headers to send with the :class:`Request`.
        :param cookies: (optional) Dict or CookieJar object to send with the :class:`Request`.
        :param files: (optional) Dictionary of 'filename': file-like-objects for multipart encoding upload.
        :param auth: (optional) Auth tuple to enable Basic/Digest/Custom HTTP Auth.
        :param timeout: (optional) Float describing the timeout of the request.
        :param allow_redirects: (optional) Boolean. Set to True if POST/PUT/DELETE redirect following is allowed.
        :param proxies: (optional) Dictionary mapping protocol to the URL of the proxy.
        :param return_response: (optional) If False, an un-sent Request object will returned.
        :param config: (optional) A configuration dictionary.
        :param prefetch: (optional) if ``True``, the response content will be immediately downloaded.
        :param verify: (optional) if ``True``, the SSL cert will be verified. A CA_BUNDLE path can also be provided.
        """

```

由于我们只传进来url和method两个参数，我们就看这两个参数相关的内容。

```python
# Default empty dicts for dict params.
        cookies = {} if cookies is None else cookies
        data = {} if data is None else data
        files = {} if files is None else files
        headers = {} if headers is None else headers
        params = {} if params is None else params
        hooks = {} if hooks is None else hooks

        if verify is None:
            verify = self.verify

        # use session's hooks as defaults
        for key, cb in self.hooks.iteritems():
            hooks.setdefault(key, cb)

        # Expand header values.
        if headers:
            for k, v in headers.items() or {}:
                headers[k] = header_expand(v)

        args = dict(
            method=method,
            url=url,
            data=data,
            params=params,
            headers=headers,
            cookies=cookies,
            files=files,
            auth=auth,
            hooks=hooks,
            timeout=timeout,
            allow_redirects=allow_redirects,
            proxies=proxies,
            config=config,
            verify=verify,
            _poolmanager=self.poolmanager
        )
```

首先对可能用到的参数进行了设置，其中大部分都是空字典。

```python
    __attrs__ = [
        'headers', 'cookies', 'auth', 'timeout', 'proxies', 'hooks',
        'params', 'config']
    
    for attr in self.__attrs__:
            session_val = getattr(self, attr, None)  # 获取__attrs__列出属性的值。也是字典
            local_val = args.get(attr)

            args[attr] = merge_kwargs(local_val, session_val) # 将args中的值更新到实例的属性值之中，并将属性值的引用返回
```

然后对参数进行清理，空值的删掉。



```python
# Arguments manipulation hook.
args = dispatch_hook('args', args['hooks'], args)

# Create the (empty) response.
r = Request(**args)   # 看到在这里创建了一个Request对象。前面那个request方法说创建一个request对象，到现在才创建。

# Give the response some context.
r.session = self

# Don't send if asked nicely.
if not return_response:
	return r

# Send the HTTP Request.
r.send(prefetch=prefetch)

# Send any cookies back up the to the session.
self.cookies.update(r.response.cookies)

# Return the response.
return r.response
```

再看看Request函数

```python
class Request(object):
    """The :class:`Request <Request>` object. It carries out all functionality of
    Requests. Recommended interface is with the Requests functions.
    """

    def __init__(self,
        url=None,
        headers=dict(),
        files=None,
        method=None,
        data=dict(),
        params=dict(),
        auth=None,
        cookies=None,
        timeout=None,
        redirect=False,
        allow_redirects=False,
        proxies=None,
        hooks=None,
        config=None,
        _poolmanager=None,
        verify=None):

        #: Float describes the timeout of the request.
        #  (Use socket.setdefaulttimeout() as fallback)
        self.timeout = timeout

        #: Request URL.
        self.url = url

        #: Dictionary of HTTP Headers to attach to the :class:`Request <Request>`.
        self.headers = dict(headers or [])

        #: Dictionary of files to multipart upload (``{filename: content}``).
        self.files = files

        #: HTTP Method to use.
        self.method = method

        #: Dictionary or byte of request body data to attach to the
        #: :class:`Request <Request>`.
        self.data = None

        #: Dictionary or byte of querystring data to attach to the
        #: :class:`Request <Request>`.
        self.params = None
        self.params = dict(params or [])

        #: True if :class:`Request <Request>` is part of a redirect chain (disables history
        #: and HTTPError storage).
        self.redirect = redirect

        #: Set to True if full redirects are allowed (e.g. re-POST-ing of data at new ``Location``)
        self.allow_redirects = allow_redirects

        # Dictionary mapping protocol to the URL of the proxy (e.g. {'http': 'foo.bar:3128'})
        self.proxies = dict(proxies or [])

        self.data, self._enc_data = self._encode_params(data)
        self.params, self._enc_params = self._encode_params(params)

        #: :class:`Response <Response>` instance, containing
        #: content and metadata of HTTP Response, once :attr:`sent <send>`.
        self.response = Response()

        #: Authentication tuple or object to attach to :class:`Request <Request>`.
        self.auth = auth

        #: CookieJar to attach to :class:`Request <Request>`.
        self.cookies = dict(cookies or [])

        #: Dictionary of configurations for this request.
        self.config = dict(config or [])

        #: True if Request has been sent.
        self.sent = False

        #: Event-handling hooks.
        self.hooks = hooks

        #: Session.
        self.session = None

        #: SSL Verification.
        self.verify = verify

        if headers:
            headers = CaseInsensitiveDict(self.headers)
        else:
            headers = CaseInsensitiveDict()

        # Add configured base headers.
        for (k, v) in self.config.get('base_headers', {}).items():
            if k not in headers:
                headers[k] = v

        self.headers = headers
        self._poolmanager = _poolmanager

        # Pre-request hook.
        r = dispatch_hook('pre_request', hooks, self)
        self.__dict__.update(r.__dict__)


    def __repr__(self):
        return '<Request [%s]>' % (self.method)


    def _build_response(self, resp, is_error=False):
        """Build internal :class:`Response <Response>` object
        from given response.
        """

        def build(resp):

            response = Response()

            # Pass settings over.
            response.config = self.config

            if resp:

                # Fallback to None if there's no status_code, for whatever reason.
                response.status_code = getattr(resp, 'status', None)

                # Make headers case-insensitive.
                response.headers = CaseInsensitiveDict(getattr(resp, 'headers', None))

                # Set encoding.
                response.encoding = get_encoding_from_headers(response.headers)

                # Start off with our local cookies.
                cookies = self.cookies or dict()

                # Add new cookies from the server.
                if 'set-cookie' in response.headers:
                    cookie_header = response.headers['set-cookie']
                    cookies = oreos.dict_from_string(cookie_header)

                # Save cookies in Response.
                response.cookies = cookies

                # No exceptions were harmed in the making of this request.
                response.error = getattr(resp, 'error', None)

            # Save original response for later.
            response.raw = resp

            if is_error:
                response.error = resp

            response.url = self.full_url

            return response

        history = []

        r = build(resp)
        cookies = self.cookies
        self.cookies.update(r.cookies)

        if r.status_code in REDIRECT_STATI and not self.redirect:

            while (
                ('location' in r.headers) and
                ((r.status_code is codes.see_other) or (self.allow_redirects))
            ):

                if not len(history) < self.config.get('max_redirects'):
                    raise TooManyRedirects()

                history.append(r)

                url = r.headers['location']

                # Handle redirection without scheme (see: RFC 1808 Section 4)
                if url.startswith('//'):
                    parsed_rurl = urlparse(r.url)
                    url = '%s:%s' % (parsed_rurl.scheme, url)

                # Facilitate non-RFC2616-compliant 'location' headers
                # (e.g. '/path/to/resource' instead of 'http://domain.tld/path/to/resource')
                if not urlparse(url).netloc:
                    url = urljoin(r.url, url)

                # http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.3.4
                if r.status_code is codes.see_other:
                    method = 'GET'
                else:
                    method = self.method

                # Remove the cookie headers that were sent.
                headers = self.headers
                try:
                    del headers['Cookie']
                except KeyError:
                    pass

                request = Request(
                    url=url,
                    headers=headers,
                    files=self.files,
                    method=method,
                    params=self.session.params,
                    auth=self.auth,
                    cookies=cookies,
                    redirect=True,
                    config=self.config,
                    timeout=self.timeout,
                    _poolmanager=self._poolmanager,
                    proxies = self.proxies,
                    verify = self.verify
                )

                request.send()
                cookies.update(request.response.cookies)
                r = request.response
                self.cookies.update(r.cookies)

            r.history = history

        self.response = r
        self.response.request = self
        self.response.cookies.update(self.cookies)


    @staticmethod
    def _encode_params(data):
        """Encode parameters in a piece of data.

        If the data supplied is a dictionary, encodes each parameter in it, and
        returns a list of tuples containing the encoded parameters, and a urlencoded
        version of that.

        Otherwise, assumes the data is already encoded appropriately, and
        returns it twice.
        """

        if hasattr(data, '__iter__'):
            data = dict(data)  # 鸭子类型，是不是可迭代的，是就，转为字典

        if hasattr(data, 'items'):
            result = []
            for k, vs in data.items():
                for v in isinstance(vs, list) and vs or [vs]:
                    result.append((k.encode('utf-8') if isinstance(k, unicode) else k,
                                   v.encode('utf-8') if isinstance(v, unicode) else v))
            return result, urllib.urlencode(result, doseq=True)
        else:
            return data, data

    @property
    def full_url(self):
        """Build the actual URL to use."""

        if not self.url:
            raise URLRequired()

        # Support for unicode domain names and paths.
        scheme, netloc, path, params, query, fragment = urlparse(self.url)

        if not scheme:
            raise ValueError()

        netloc = netloc.encode('idna')

        if isinstance(path, unicode):
            path = path.encode('utf-8')

        path = requote_path(path)

        url = str(urlunparse([ scheme, netloc, path, params, query, fragment ]))

        if self._enc_params:
            if urlparse(url).query:
                return '%s&%s' % (url, self._enc_params)
            else:
                return '%s?%s' % (url, self._enc_params)
        else:
            return url

    @property
    def path_url(self):
        """Build the path URL to use."""

        url = []

        p = urlsplit(self.full_url)

        # Proxies use full URLs.
        if p.scheme in self.proxies:
            return self.full_url

        path = p.path
        if not path:
            path = '/'
        url.append(path)

        query = p.query
        if query:
            url.append('?')
            url.append(query)

        return ''.join(url)



    def send(self, anyway=False, prefetch=False):
        """Sends the request. Returns True of successful, false if not.
        If there was an HTTPError during transmission,
        self.response.status_code will contain the HTTPError code.

        Once a request is successfully sent, `sent` will equal True.

        :param anyway: If True, request will be sent, even if it has
        already been sent.
        """

        # Build the URL
        url = self.full_url

        # Logging
        if self.config.get('verbose'):
            self.config.get('verbose').write('%s   %s   %s\n' % (
                datetime.now().isoformat(), self.method, url
            ))

        # Nottin' on you.
        body = None
        content_type = None

        # Multi-part file uploads.
        if self.files:
            if not isinstance(self.data, basestring):

                try:
                    fields = self.data.copy()
                except AttributeError:
                    fields = dict(self.data)

                for (k, v) in self.files.items():
                    # support for explicit filename
                    if isinstance(v, (tuple, list)):
                        fn, fp = v
                    else:
                        fn = guess_filename(v) or k
                        fp = v
                    fields.update({k: (fn, fp.read())})

                (body, content_type) = encode_multipart_formdata(fields)
            else:
                pass
                # TODO: Conflict?
        else:
            if self.data:

                body = self._enc_data
                if isinstance(self.data, basestring):
                    content_type = None
                else:
                    content_type = 'application/x-www-form-urlencoded'

        # Add content-type if it wasn't explicitly provided.
        if (content_type) and (not 'content-type' in self.headers):
            self.headers['Content-Type'] = content_type

        if self.auth:
            if isinstance(self.auth, tuple) and len(self.auth) == 2:
                # special-case basic HTTP auth
                self.auth = HTTPBasicAuth(*self.auth)

            # Allow auth to make its changes.
            r = self.auth(self)

            # Update self to reflect the auth changes.
            self.__dict__.update(r.__dict__)

        _p = urlparse(url)
        proxy = self.proxies.get(_p.scheme)

        if proxy:
            conn = poolmanager.proxy_from_url(proxy)
            _proxy = urlparse(proxy)
            if '@' in _proxy.netloc:
                auth, url = _proxy.netloc.split('@', 1)
                self.proxy_auth = HTTPProxyAuth(*auth.split(':', 1))
                r = self.proxy_auth(self)
                self.__dict__.update(r.__dict__)
        else:
            # Check to see if keep_alive is allowed.
            if self.config.get('keep_alive'):
                conn = self._poolmanager.connection_from_url(url)
            else:
                conn = connectionpool.connection_from_url(url)

        if url.startswith('https') and self.verify:

            cert_loc = None

            # Allow self-specified cert location.
            if self.verify is not True:
                cert_loc = self.verify


            # Look for configuration.
            if not cert_loc:
                cert_loc = os.environ.get('REQUESTS_CA_BUNDLE')

            # Curl compatiblity.
            if not cert_loc:
                cert_loc = os.environ.get('CURL_CA_BUNDLE')

            # Use the awesome certifi list.
            if not cert_loc:
                cert_loc = __import__('certifi').where()

            conn.cert_reqs = 'CERT_REQUIRED'
            conn.ca_certs = cert_loc

        if not self.sent or anyway:

            if self.cookies:

                # Skip if 'cookie' header is explicitly set.
                if 'cookie' not in self.headers:

                    # Simple cookie with our dict.
                    c = oreos.monkeys.SimpleCookie()
                    for (k, v) in self.cookies.items():
                        c[k] = v

                    # Turn it into a header.
                    cookie_header = c.output(header='', sep='; ').strip()

                    # Attach Cookie header to request.
                    self.headers['Cookie'] = cookie_header

            try:
                # The inner try .. except re-raises certain exceptions as
                # internal exception types; the outer suppresses exceptions
                # when safe mode is set.
                try:
                    # Send the request.
                    r = conn.urlopen(
                        method=self.method,
                        url=self.path_url,
                        body=body,
                        headers=self.headers,
                        redirect=False,
                        assert_same_host=False,
                        preload_content=False,
                        decode_content=False,
                        retries=self.config.get('max_retries', 0),
                        timeout=self.timeout,
                    )
                    self.sent = True

                except MaxRetryError, e:
                    raise ConnectionError(e)

                except (_SSLError, _HTTPError), e:
                    if self.verify and isinstance(e, _SSLError):
                        raise SSLError(e)

                    raise Timeout('Request timed out.')

            except RequestException, e:
                if self.config.get('safe_mode', False):
                    # In safe mode, catch the exception and attach it to
                    # a blank urllib3.HTTPResponse object.
                    r = HTTPResponse()
                    r.error = e
                else:
                    raise

            self._build_response(r)

            # Response manipulation hook.
            self.response = dispatch_hook('response', self.hooks, self.response)

            # Post-request hook.
            r = dispatch_hook('post_request', self.hooks, self)
            self.__dict__.update(r.__dict__)

            # If prefetch is True, mark content as consumed.
            if prefetch:
                # Save the response.
                self.response.content

            return self.sent


```

request的send函数

```python
    def send(self, anyway=False, prefetch=False):
        """Sends the request. Returns True of successful, false if not.
        If there was an HTTPError during transmission,
        self.response.status_code will contain the HTTPError code.

        Once a request is successfully sent, `sent` will equal True.

        :param anyway: If True, request will be sent, even if it has
        already been sent.
        """
```



