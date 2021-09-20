# Performance testing 
## Java
- https://devwithus.com/install-java-windows-10/
- Download the installer
## Gatling
- https://gatling.io/get-started/
- Download normal one (not enterprise)
## IntelliJ
- https://www.jetbrains.com/idea/download/#section=windows
- Download community version
## Scala
#### Setting up testing env
- In IntelliJ (with admin permissions), navigate to plugins: (`CTRL+Alt+S`) -> `install scala plugin`

### Running the test with node app

1. Make sure the app is running  
- ssh into app machine, add DB_HOST environment variable, and npm start
2. Open the page in the browser  
3. Record visiting the posts page  
- Right-click, then click `Inspect`
- Click `Network`, then click the `Clear` icon (a circle with a line through it)
- Click the `Record` button (beside it), then navigate to the /posts page
- Click the `Download` button in the `Network` tab
- Save the HAR file
4. Record visiting the fibonacci page  
- As in step 3
5. Record going back to the home page  
- As in step 3
6. Convert the HAR files to Scala files using recorder.bat  
- In IntelliJ terminal, `cd ~/gatling-charts-highcharts-bundle-3.6.1-bundle/gatling-charts-highcharts-bundle-3.6.1/bin`
- `./recorder.bat`
- In the top right-hand corner of the page that comes up, click `HAR converter`
- Click `Browse`, and select your newly-saved HAR file
- Give it a `Class Name`, then click `Start`
7. Run each one  
- `./gatling.bat`
- Select the number that corresponds with the class name for your new HAR file
- Give it a description
- When the test completes, copy the html file path it outputs at the end, and paste it into the browser -> this displays the various metrics related to how the test performed
8. Run each one with 10% more visitors  
- And enjoy watching them fail!