# Disk Fusion

Disk 

## Installation
The DiskProvider will read and provide all disks with the properties of disks in the config.
Of course, you may configure as many disks as you want, and may even have multiple disks that use the same driver.
The default disk provider with the name is configured. DiskManager supports the default local driver. See also the api config at the bottom of the page
For example config
```js
{
    default: 'myDisk',
    disks  : {
        myDisk: {
            driver    : 'local',
            directory : path.join(__dirname, '..', 'public')
        },
        ...
    },
}
```

Remember to use the disk service in the file manifest
```js
export * from 'Fusion/disk';
```

### Usage
DiskManagerInterface provides default in Fusion
Using @singleton get an entity of DiskManger

You have to use Disk Manager get an entity or register new Disk
```js 
import { DiskManagerInterface }  from "Fusion";

@singleton(DiskManagerInterface)
@controller()
class Controller {
    
    constructor(diskManager) {
        // The disk manager is available here
        this.myDisk = diskManager.disk() // like diskManager.disk('myDisk')
        // If the parameter name in the [disk] action does not exist, Disk Manager will provide the default name
    }
    
    saveFile() {
        let someReadSteam ....;
        
        this.myDisk.createWriteStream('fileName').pipe(someReadSteam)
    }
    
    saveTextFile() {
        this.myDisk.createWriteStream('fileName').pipe(someReadSteam)
    }
}
```

## Save File 
createWriteStreamm(nameFile) returns WriteableStream
Create a WriteableStream stream ready to listen to write data
You can save a string like the example below
```js
// Save the ReadableSteam with a file named fileName
let someReadableStream ....;
myDisk.createWriteStream('fileName').pipe(someReadableStream);
// Save the 'somthing content' with a file named fileName.txt in directory dir/dir2
myDisk.createWriteStream('dir/dir2/fileName.txt').end('something content');
```
## Check For An Existing File
exists(nameFile) returns Promise<boolean>
The method will check the existing file
```js
myDisk.exists('fileName.txt').then(fileExits => {
    // fileExits is boolean
});
```
## Delete File
exists(nameFile) returns Promise<boolean>
The method will delete the existing file
```js
myDisk.delete('fileName.txt').then(fileExits => {
    // File has been deleted
});
```
## Get File
createReadStream(nameFile) returns ReadableStream
Create a readable stream to read the contents of the remote file. It can be piped to a writable stream or listened to for 'data' events to read a file's contents.

```js
let someWriteableStream ....;
someWriteableStream.pipe(myDisk.createReadStream('fileName.tx'));
```
## Custom disk

In order to set up the custom my disk you will need a disk. Let's add a class and implements DiskInterface from Fusion:

```js
import { DiskInterface } from 'Fusion';
/**
 * @implements DiskInterface
 */
export default class S3Disk {
...
```
Next, you should create a service provider such as S3ServiceProvider. In the provider's  boot method, you may use the DiskManagerInterface extend method to define the custom driver:

```js
import { DiskManagerInterface } from 'Fusion';
@provider()
export class S3ServiceProvider {

    constructor(container) {
        this.container = container;
    }
    async boot() {
        let diskManager = await this.container.make(DiskManagerInterface);
        diskManager.extend('s3', (config) => new S3Disk(config));
    }
}
```








