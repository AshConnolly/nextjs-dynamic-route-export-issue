# Dynamic routes and export static not working

I have a site that is statically exported, and has dynamic routes.

Inside the dynamic routes I’m grabbing the router.query.id value, and doing client side fetching with it (I'm using client side fetching as the app is exported to static files).

Taking the [next.js static example](https://github.com/vercel/next.js/tree/canary/examples/with-static-export) as a starting point, I've edited `pages/post/[id].tsx` file by removing the `getStaticPaths` and `getStaticProps` (as I don’t require build time static generation) and I've added clientside fetching on mount:

```tsx
import Head from 'next/head'
import Link from 'next/link'
import { useRouter } from 'next/router'
import { useEffect, useState } from 'react'
import { GetPost } from '../../lib/postdata_api'
import { PostData } from '../../types/postdata'

const Post = () => {
  const [postData, setPostData] = useState<null | PostData>(null)
  const router = useRouter()
  const id = router.query.id as string

  useEffect(() => {
    if (!router.isReady) return
    GetPost(id).then(r => setPostData(r))
  }, [router.isReady, id])

  if (!postData) return 'Loading...'

  return (
    <main>
      <Head>
        <title>{postData.title}</title>
      </Head>

      <h1>{postData.title}</h1>

      <p>{postData.body}</p>

      <Link href="/">
        <a>Go back to home</a>
      </Link>
    </main>
  )
}

export default Post
```

Now this will work fine if I run it in dev mode.

However if I build & export it, and then [serve](https://www.npmjs.com/package/serve) the static files it doesn’t work.

You can go to the site root and click on a link, which will take you to a post page like [`http://localhost:3000/post/1`](http://localhost:3000/post/1) but if you refresh the page / visit it directly, it does not work.

**Why is this? Do dynamic routes, that need to access the router query object, not work when statically exported?**

///////////////// Orignal Readme 👇

# Static export example

This example show how to export to static HTML files your Next.js application fetching data from an API to generate a dynamic list of pages.

When trying to run `npm start` it will build and export your pages into the `out` folder and serve them on `localhost:5000`.

## Deploy your own

Deploy the example using [Vercel](https://vercel.com?utm_source=github&utm_medium=readme&utm_campaign=next-example) or preview live with [StackBlitz](https://stackblitz.com/github/vercel/next.js/tree/canary/examples/with-static-export)

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/git/external?repository-url=https://github.com/vercel/next.js/tree/canary/examples/with-static-export)

## How to use

Execute [`create-next-app`](https://github.com/vercel/next.js/tree/canary/packages/create-next-app) with [npm](https://docs.npmjs.com/cli/init), [Yarn](https://yarnpkg.com/lang/en/docs/cli/create/), or [pnpm](https://pnpm.io) to bootstrap the example:

```bash
npx create-next-app --example with-static-export with-static-export-app
```

```bash
yarn create next-app --example with-static-export with-static-export-app
```

```bash
pnpm create next-app --example with-static-export with-static-export-app
```
