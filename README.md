hexpub: <img src="https://git-scm.com/images/logos/downloads/Git-Icon-1788C.png" width="30px" height="30px"> + <img src="https://cdn.travis-ci.com/images/logos/TravisCI-Mascot-1-20feeadb48fc2492ba741d89cb5a5c8a.png" width="30px" height="30px"> + ![Hex](https://avatars3.githubusercontent.com/u/6621265?v=3&s=30) = :+1:
=======

Keeping our packages up to date on [Hex.pm](https://hex.pm) is something we all
forget too easily. This little script and Travis CI configuration automates 
publishing when you tag your Git repository.
 
Because Travis CI has no Hex.pm [deployment provider](https://docs.travis-ci.com/user/deployment/) 
(yet), we’ll have to do some more work. This solution is based on 
[an article by Tristan Sloughter](http://blog.erlware.org/automatic-hex-package-publishing-with-travis-ci/).  

Let’s go!
---------

1. Add a deployment section to your project’s `.travis.yml`:
 
    ```yml
    language: erlang
    otp_release:
      - 19.3
      # ...
    # ...

    jobs:
      include:
        - stage: deploy
          otp_release: 19.3
          script: skip
          deploy:
            provider: script
            script: 
              - wget https://raw.githubusercontent.com/zotonic/hexpub/master/hexpub.sh 
              - chmod +x hexpub.sh 
              - ./hexpub.sh
            skip_cleanup: true
            on:
              tags: true
              all_branches: true
    ```

2. Log in to [Travis CI](https://travis-ci.org), and under your project’s 
   settings, add two environment variables:
   - `HEX_USERNAME` is your Hex.pm username
   - `HEX_KEY` is your Hex.pm API key, which you can find locally with 
      `$ rebar3 hex config key`.

3. In your project’s `app.src`, enter `{vsn, "git"}`:

    ```erlang
    {application, your_app, [
        {description, "This app is awesome"},
        {vsn, "git"}
        %% ...
    ]}.
    ```

Et voilà
--------
  
Now you can tag your project and auto-publish to Hex:

```bash
$ git tag 1.0.0
$ git push origin 1.0.0
```
