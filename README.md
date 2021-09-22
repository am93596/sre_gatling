# Diagram of full infrastructure so far
![241556504_353893073182767_944237784430600497_n](https://user-images.githubusercontent.com/88166874/134310351-092e0bac-cfe5-47f7-9cc1-773410570554.jpg)

### Load Balancer Overview Diagram
![load-balancer-img](https://user-images.githubusercontent.com/88166874/134197989-d46a62fb-4552-4671-9bea-9604c7caec84.png)

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
![test-with-1-user](https://user-images.githubusercontent.com/88166874/134039241-bd3b7413-0272-4828-89e3-c7647b2d1670.PNG)  

8. Run each one with 10% more visitors  
- And enjoy watching them fail!
![test-with-10-users](https://user-images.githubusercontent.com/88166874/134039282-5b46276b-bc19-40ec-954b-c2895f42dee6.PNG)  
![test-with-100-users](https://user-images.githubusercontent.com/88166874/134039316-92a9bdb9-7559-47bb-b55e-e67ece3f3b26.PNG)  
![test-with-1000-users](https://user-images.githubusercontent.com/88166874/134039329-b5322dca-76ed-4f2b-8d58-3a6b307c8448.PNG)

### What I want to monitor
- CPU utilisation
- Network In
- RequestCountPerTarget

