# actions-atop

This repository is to provide an example for how to install, configure, and download historical process data from [Atop](https://github.com/Atoptool/atop) when using GitHub Actions runners with Ubuntu Linux. 

Please check [atop.yml](https://github.com/andrewgxp/actions-atop/blob/master/atop.yml) for a full example. 

### How to:

1. Add the following code to be beginning of your GitHub Actions Workflow: 

```
    - name: Install Atop
      run: sudo apt-get update && sudo apt-get install atop -y 
      
    - name: Configure Atop
      run: sudo sed -i 's#INTERVAL=600#INTERVAL=5#g' /usr/share/atop/atop.daily
      
    - name: Restart Atop
      run: sudo systemctl restart atop
```

2. Then add this code to the end of your GitHub Actions Workflow: 

```
    - name: Tar the atop logs
      run: tar -cvzf atop.tar.gz /var/log/atop/
      
    - uses: actions/upload-artifact@v2
      with:	
        name: 'atop_logs'
        path: ${{ github.workspace }}/atop.tar.gz
```

3. [Find and download your loads](https://github.com/actions/upload-artifact#where-does-the-upload-go) 

To view these logs you will need to install atop on your local Linux desktop or VM. 

[How analyze your processes using atop](https://www.redhat.com/sysadmin/analyzing-linux-server-performance-atop)

Results: 

![results](https://i.imgur.com/rApHeAZ.png)


### Troubleshooting: 

You may run into compatibility issues between the atop version on your GitHub runner and your desktop or VM.

Error: 

```
(created by version 2.3 - current version 2.4)
trying to activate atop-2.3....
activation of atop-2.3 failed!
```

This can be resolved using the `atopconvert` utility that is installed with `atop`. 

Example: `atopconvert atop_20200430 atop-latest -t 2.4`

