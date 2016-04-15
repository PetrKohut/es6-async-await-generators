# "Async await" with generators in express app

```js
'use strict'

const express = require('express')
const co = require('co')

const wrap = (genFn) => {
    const cr = co.wrap(genFn)
    return (req, res, next) => {
        cr(req, res, next).catch(next)
    }
}

const app = express()

app.get('/', wrap(function *(req, res) {
	const result = yield Promise.resolve('Hello async await')
	res.json({ message: result })
}))

app.get('/error', wrap(function *(req, res) {
	const result = yield Promise.reject(new Error('Something went wrong.'))
	res.json({ message: 'This will never happen' })
}))

app.use((err, req, res, next) => {
	res.json({ error: err.message })
})

app.listen(3000, () => {
  console.log('Example app listening on port 3000!')
})
```

Inspiration: https://strongloop.com/strongblog/async-error-handling-expressjs-es7-promises-generators/
