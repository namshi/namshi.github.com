## How to Run it

1. Do `fig build`
2. then `fig run --service-ports web`
3. To create new post, run `rake new_post["Awesome Title"]`
4. inside the container `rake generate` then `rake preview`
5. access it `localhost:4000` or `machineIP:4000`.

### Deploying

After you have tested that the pages have been merged to the `source` branch

* Setup the github pages again
  ```
  rake setup_github_pages
  ```

* Generate the pages again and deploy
  ```
  rake generate && rake deploy
  ```

## Namshi tech blog

This is the code for [tech.namshi.io](http://tech.namshi.io), built with [Octopress](http://octopress.org/).
