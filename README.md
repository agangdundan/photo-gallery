Lab 1: Introduction to Ionic, Capacitor and Angular

In this assignment you will create a photo gallery app that will allow users to take photos. The app will save the photos to the file system and also display the photos in a gallery. You will build the app in such a way that it will run on Android, iOS and the web!

Part A
1.	First, we will download and install Node.js. We are using Node.js for it’s package manager which makes it really easy to download/import useful tools and code to use in our app. Go to https://nodejs.org/en/ and download the LTS version.

2.	When the download is complete, run the downloaded setup file (when the setup wizard opens, keep all the default settings and click next until the install starts).

3.	When the installation finishes, click the Windows button and find/run Node.js (once you run it you can close the window it opens, this just ensures the Node.js service is running).

4.	You are now ready to start setting up the development environment. Click the Windows button in the bottom right of your screen and then find/run Visual Studio Code (VS Code). If you have not done so, please install Visual Studio Code.

5.	To install the tools for our cross-platform app we will be using commands at the command line
(a.k.a. terminal). When VS Code opens, click “Terminal” from the top menu, then “New
Terminal”. This will open a terminal in the bottom of your VS Code development environment
where we can type commands.
 
 
6.	Before you start setting up your tools, create a folder for your apps. Be sure to navigate into the folder you create on your file system before continuing to the next step.


7.	Run the following command to install Ionic:

npm install -g @ionic/cli native-run cordova-res

NOTE: if you receive an error stating scripts are not enabled, open PowerShell in elevated mode and runt he following command:
•	Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy Unrestricted

NOTE: if you receive an EACCES error, please go here for help:
•	https://ionicframework.com/docs/developing/tips#resolving-permission-errors

8.	Run the following command to create an app using a template so you have something to start from (this will create all the code you need for an app, we will learn about all the code it creates over the coming weeks):

NOTE: you do not have to create an Ionic account when asked.

ionic start photo-gallery tabs --type=angular --capacitor

9.	Now we can open our app in VS Code. Do this by clicking “File”, then “Open Folder” and select the “photo-gallery” folder that was created by the previous command.

10.	After you open the project/app folder, VS Code will refresh and you will have to open the terminal again (refer to step 5 if you forget how).

NOTE: you may be asked if you trust the publishers of the project tools, say yes.

11.	First, let’s install some libraries/plugins so we do not have to write all the code for our app (one of the best things about cross-platform tools!) Run the following command:

npm install @capacitor/camera @capacitor/storage @capacitor/filesystem

12.	Now, let’s install the PWA dependency so we can produce our app as a PWA app, run the
following command:

npm install @ionic/pwa-elements
 
13.	Next, import @ionic/pwa-elements by editing src/main.ts

import { enableProdMode } from '@angular/core';
…
import { defineCustomElements } from '@ionic/pwa-elements/loader';
…
platformBrowserDynamic().bootstrapModule(AppModule)
.catch(err => console.log(err));

// Call the element loader after the platform has been bootstrapped defineCustomElements(window);

14.	That’s it, we can now run our app by running this command:

ionic serve

15.	Before we start building our app, let’s hook our lab assignment up to GitHub. Go over to GitHub
and create a repository for this assignment.

16.	After you create your repository, go back to VS Code, click the source control button, then enter a message and perform a commit by clicking the checkmark button.
 

17.	Next we will push to our remote repository. Click the three dots to open the menu then click
“Push”.
 
 
18.	A message will show advising you there are no remote repositories connected to this project,
click the “Add Remote” button to tell it where your remote repository is.
 

19.	A window will show where you can enter your GitHub repository URL. Go over to GitHub, copy the repository URL, then come back to VS Code and paste it in the box, then press the enter key.
 

20.	It will then ask you to enter a repository name, enter “origin” (without the double quotes).
 

21.	You have now setup your remote, do a push again by clicking the three dots and then “Push” (note: you may be asked for your username and personal access token if this is your first time pushing to GitHub from VS Code).
 

22.	Alright, let’s get to building our app! Navigate to the file /src/app/tab2/tab2.page.html

<ion-header [translucent]="true">
<ion-toolbar>
<ion-title>
Tab 2
Photo Gallery
</ion-title>
</ion-toolbar>
</ion-header>
 
23.	Now add a floating button.

<ion-content>
  <ion-header collapse="condense">
    <ion-toolbar>
 	<ion-title size="large">Tab 2</ion-title>
    </ion-toolbar>
  </ion-header>
<ion-fab vertical="bottom" horizontal="center" slot="fixed">
<ion-fab-button>
<ion-icon name="camera"></ion-icon>
</ion-fab-button>
</ion-fab>
</ion-content>

24.	Next, open src/app/tabs/tabs.page.html. Change the label to “Photos” and the icon name to “images”

<ion-tab-button tab="tab2">
<ion-icon name="ellipse"></ion-icon>
<ion-label>Tab 2</ion-label>
<ion-icon name="images"></ion-icon>
<ion-label>Photos</ion-label>
</ion-tab-button>

25.	Update tab 1 by adding 3 UI components of your choice (go to the Ionic documentation and choose: https://ionicframework.com/docs/components)

26.	Run ionic serve and check out your app!

27.	Always do a commit and push to ensure your code is safe in your repository. You will not need to submit this lab until part b is complete.
 
Part B

1.	All Capacitor logic (Camera usage and other native features) will be encapsulated in a service class. Create PhotoService using the ionic generate command:

ionic g service services/photo

2.	Open the new services/photo.service.ts file, and let’s add the logic that will power the camera functionality. First, import Capacitor dependencies and get references to the Camera, Filesystem, and Storage plugins:

import { Injectable } from '@angular/core';
import { Camera, CameraResultType, CameraSource, Photo } from '@capacitor/camera'; import { Filesystem, Directory } from '@capacitor/filesystem';
import { Storage } from '@capacitor/storage';

3.	Next, define a new class method, addNewToGallery, that will contain the core logic to take a
device photo and save it to the filesystem. Let’s start by opening the device camera:

constructor() { }

public async addNewToGallery() {
// Take a photo
const capturedPhoto = await Camera.getPhoto({ resultType: CameraResultType.Uri,
source: CameraSource.Camera, quality: 100
});
}
}

4.	Notice the magic here: there's no platform-specific code (web, iOS, or Android)! The Capacitor Camera plugin abstracts that away for us, leaving just one method call - Camera.getPhoto() - that will open up the device's camera and allow us to take photos.

5.	Next, open up tab2.page.ts and import the PhotoService class and add a method that calls the addNewToGallery method on the imported service:

import { Component } from '@angular/core';
import { PhotoService } from '../services/photo.service';
…
constructor(public photoService: PhotoService) { }

addPhotoToGallery() { this.photoService.addNewToGallery();
}
 
6.	Then, open tab2.page.html and call the addPhotoToGallery() function when the FAB is tapped/clicked:

<ion-content>
<ion-fab vertical="bottom" horizontal="center" slot="fixed">
<ion-fab-button (click)="addPhotoToGallery()">
<ion-icon name="camera"></ion-icon>
</ion-fab-button>
</ion-fab>
</ion-content>

7.	Save the file, and if it's not running already, restart the development server in your browser by running ionic serve. On the Photo Gallery tab, click the Camera button. If your computer has a webcam of any sort, a modal window appears. Take a selfie! After taking a photo, it disappears right away. We need to display it within our app and save it for future access. We will do this in part b of the lab.

8.	Back over in our photo.service.ts file, outside of the PhotoService class definition (the very bottom of the file), create a new interface, Photo, to hold our photo metadata:

…
public async addNewToGallery() {
// Take a photo
const capturedPhoto = await Camera.getPhoto({ resultType: CameraResultType.Uri,
source: CameraSource.Camera, quality: 100
});
}
} //end of class here

interface GalleryPhoto { filepath: string; webviewPath: string;
}

9.	Back at the top of the file, define an array of Photos, which will contain a reference to each photo captured with the Camera.

…
export class PhotoService {
public photos: GalleryPhoto[] = [];
…
 
10.	Over in the addNewToGallery function, add the newly captured photo to the beginning of the Photos array.

…
const capturedPhoto = await Camera.getPhoto({ resultType: CameraResultType.Uri,
source: CameraSource.Camera, quality: 100
});

this.photos.unshift({ filepath: "soon...",
webviewPath: capturedPhoto.webPath
});
…

11.	Next, move over to tab2.page.html so we can display the image on the screen. Add a Grid component so that each photo will display nicely as photos are added to the gallery, and loop through each photo in the PhotoServices's Photos array, adding an Image component (<ion-
img>) for each. Point the src (source) at the photo’s path:

…
<ion-content [fullscreen]="true">
<ion-grid>
<ion-row>
<ion-col size="6" *ngFor="let photo of photoService.photos; index as position">
<ion-img src="{{ photo.webviewPath }}"></ion-img>
</ion-col>
</ion-row>
</ion-grid>
…

12.	Save all files. Within the web browser, click the Camera button and take another photo. This time, the photo is displayed in the Photo Gallery!

13.	Fortunately, saving them to the filesystem only takes a few steps. Begin by creating a new class method, savePicture(), in the PhotoService class (src/app/services/photo.service.ts). We pass in the cameraPhoto object, which represents the newly captured device photo:

…
private async savePicture(cameraPhoto: Photo) { }
…
 
14.	We can use this new method immediately in addNewToGallery():

…
resultType: CameraResultType.Uri, // file-based data; provides best performance source: CameraSource.Camera, // automatically take a new photo with the camera quality: 100 // highest quality (0 to 100)
});

// Save the picture and add it to photo collection
const savedImageFile = await this.savePicture(capturedPhoto); this.photos.unshift(savedImageFile);

    this.photos.unshift({
 	filepath: "soon...",
 	webviewPath: capturedPhoto.webPath
    });
}
…

15.	We’ll use the Capacitor Filesystem API to save the photo to the filesystem. To start, convert the photo to base64 format, then feed the data to the Filesystem’s writeFile function. As you’ll
recall, we display each photo on the screen by setting each image’s source path (src attribute) in tab2.page.html to the webviewPath property. So, set it then return the new GalleryPhoto object.

private async savePicture(cameraPhoto: Photo) {
// Convert photo to base64 format, required by Filesystem API to save const base64Data = await this.readAsBase64(cameraPhoto);

// Write the file to the data directory
const fileName = new Date().getTime() + '.jpeg'; const savedFile = await Filesystem.writeFile({ path: fileName,
data: base64Data, directory: Directory.Data
});

// Use webPath to display the new image instead of base64 since it's
// already loaded into memory return {
filepath: fileName,
webviewPath: cameraPhoto.webPath
};
}
 
16.	readAsBase64() is a helper function (put this function in your photo service) we’ll define next. It's useful to organize via a separate method since it requires a small amount of platform- specific (web vs. mobile) logic - more on that in a bit. For now, implement the logic for running on the web:

private async readAsBase64(cameraPhoto: Photo) {
// Fetch the photo, read as a blob, then convert to base64 format const response = await fetch(cameraPhoto.webPath!);
const blob = await response.blob();
return await this.convertBlobToBase64(blob) as string;
}

convertBlobToBase64 = (blob: Blob) => new Promise((resolve, reject) => { const reader = new FileReader;
reader.onerror = reject; reader.onload = () => {
resolve(reader.result);
};
reader.readAsDataURL(blob);
});

17.	Obtaining the camera photo as base64 format on the web appears to be a bit trickier than on mobile. In reality, we’re just using built-in web APIs: fetch() as a neat way to read the file into blob format, then FileReader’s readAsDataURL() to convert the photo blob to base64. There we go! Each time a new photo is taken, it’s now automatically saved to the filesystem.

18.	Let’s do the work to get photos loading from the filesystem. Begin by defining a constant variable that will act as the key for the store:

…
export class PhotoService { public photos: Photo[] = [];
private PHOTO_STORAGE: string = "photos";
…

19.	Next, at the end of the addNewToGallery function, add a call to Storage.set() to save the Photos array. By adding it here, the Photos array is stored each time a new photo is taken. This way, it doesn’t matter when the app user closes or switches to a different app - all photo data is saved.
…
const savedImageFile = await this.savePicture(capturedPhoto); this.photos.unshift(savedImageFile);

Storage.set({
key: this.PHOTO_STORAGE, value: JSON.stringify(this.photos)
});
 
20.	With the photo array data saved, create a function called loadSaved() that can retrieve that data. We use the same key to retrieve the photos array in JSON format, then parse it into an array:

public async loadSaved() {
// Retrieve cached photo array data
const photos = await Storage.get({ key: this.PHOTO_STORAGE }); this.photos = JSON.parse(photos.value) || [];

// more to come...
}

21.	On mobile (coming up next!), we can directly set the source of an image tag - <img src=”x” /> - to each photo file on the Filesystem, displaying them automatically. On the web, however, we must read each image from the Filesystem into base64 format, because the Filesystem API stores them in base64 within IndexedDB under the hood. Below the code you just added in the loadSaved() function, add:

public async loadSaved() {
// Retrieve cached photo array data
const photos = await Storage.get({ key: this.PHOTO_STORAGE }); this.photos = JSON.parse(photos.value) || [];

// Display the photo by reading into base64 format for (let photo of this.photos) {
// Read each saved photo's data from the Filesystem const readFile = await Filesystem.readFile({
path: photo.filepath, directory: Directory.Data
});

// Web platform only: Load the photo as base64 data photo.webviewPath = `data:image/jpeg;base64,${readFile.data}`;
}
}

22.	After, call this new method in tab2.page.ts so that when the user first navigates to Tab 2 (the Photo Gallery), all photos are loaded and displayed on the screen.
…
constructor(public photoService: PhotoService) {}

async ngOnInit() {
await this.photoService.loadSaved();
}
addPhotoToGallery() {
…
 
23.	That’s it! We’ve built a complete Photo Gallery feature in our Ionic app that works on the web. Run your app and test it out. You should be able to add a photo, restart your app, and see the photo persists. Next lab, we’ll transform it into a mobile app for iOS and Android!

24.	When you have completed this assignment commit and push your code to GitHub, then submit your repository URL and commit ID using the LMS.
