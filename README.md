# Reverse RDP into Windows on GitHub Actions (New Generation)

Ever wonder what the Desktop of the Windows Runners on GitHub Actions looks like?

![Screenshot](https://github.com/NyaMisty/reverse-rdp-windows-github-actions-ng/raw/old/screenshot.png)

This functionality is like Appveyor's RDP functionality for their Windows workers:

https://www.appveyor.com/docs/how-to/rdp-to-build-worker/

## Usage

1. Signup for an [ngrok] account & Get the tunnel auth token at: https://dashboard.ngrok.com/auth .
2. Under the repository's settings, make a repository secret called `NGROK_AUTH_TOKEN` and set it to the tunnel auth token from ngrok.
3. Embed this action in your build workflow:
  ```
  - name: Test background
    uses: NyaMisty/reverse-rdp-windows-github-actions-ng@master
    with:
      ngrok-token: ${{ secrets.NGROK_AUTH_TOKEN }}
      password: "Aa123456" # You can also put the password in secrets as you wish.
      #foreground: false
  ```
  - The foreground parameter can be used to run this action in background (i.e. non-blocking)
  - You can also run this action multiple time with different `foreground` parameter acting as workflow breakpoint
4. Visit ngrok's dashboard. https://dashboard.ngrok.com/ & Get address in ngrok's `Cloud Edge` -> `Endpoints`
6. Connect to the host and port combination with your RDP client of choice.
  - Username is always `runneradmin`, with password you specified above (defaults to `P@ssw0rd!`)
7. Enjoy! ☕
8. When you're done introspecting, delete the file `Delete-This-File-To-Continue.txt` on desktop to continue the action

These steps should be useful for debugging broken builds directly on the build worker. Use this project as reference and toss the steps into your project after some failing part of the build for introspection.

## Useful Info

* Runners can run jobs for up to 6 hours. So you have about 6 hours minus the minute setup time to poke around in these runners.
* Use 'if' expression of action step to achieve precise control of enabling RDP
  * Example1 - run RDP when failure: `if: failure()`
  * Example2 - run on manually dispatch: 
    - add input parameter under `on: workflow_dispatch:`:
      ```
      on:
        workflow_dispatch:
          inputs:
            debug_enabled:
              description: 'Run the build with ngrok debugging enabled'
              required: false
              default: false
      ```
    - `if: ${{ always() && github.event_name == 'workflow_dispatch' && github.event.inputs.debug_enabled }}`

## Similar Projects

These projects also allow remote introspection of very temporary environments like in GitHub Actions or other environments. 

* action-tmate (very mature, can spawn SSH on all runners)
  * https://github.com/mxschmitt/action-tmate
* Shell-Only (macOS, Linux, and also Windows)
  * https://tunshell.com
* macOS VNC
  * https://github.com/dakotaKat/fastmac-VNCgui

## License

MIT

[ngrok]: https://ngrok.com/