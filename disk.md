@Fusion/disk
===============

## A queue for Fusion 
 - default support local disk

## Config disk
```js
disk =  {
    default: 'myDisk',
    disks  : {
        myDisk: {
            adapter  : 'local', // type for disk
            directory: path.join(__dirname, '..', 'public')
        },
        ...
    },
};
```
## provide Disk

```js
export * from 'Fusion/Disk';
```

## get instance Disk

```js
@singleton(DiskManagerInterface)
@controller()
class Controller {
    
    constructor(diskManager) {
        
        this.myDisk = diskManager.disk('myDisk');
        this.myDisk.createWriteStream('dir1/dir2/nameFile')
        this.myDisk.createReadStream('dir1/dir2/nameFile')
        
    }
    
}
```

## custom disk 

Create new Disk
```js
/**
 * @implements DiskInterface
 * implement all method diskInterface
 */
class S3Disk {
    
    setBucket(nameBucket) {
        return this;
    }
    
    /**
    *
    * @param {String} fileName
    * @param {'public' | 'private' | String} permission
    * @return {WriteStream}
    */
    createWriteStream(fileName, permission) {
        ...
    }
    
    /**
    *
    * @param {String} fileName
    * @return {ReadableStream}
    */
    createReadStream(fileName) {
        ...
    }
    
    /**
    *
    * @param {String} fileName
    * @return {Promise<boolean>}
    */
    exists(fileName) {
        ...
    }
    
    /**
    *
    * @param {String} fileName
    * @return {Promise<boolean>}
    */
    delete(fileName) {
        ...
    }
    
}

```

Register new Type Disk
```js
@provider()
export class MyProvider {

    constructor(container) {
        this.container = container;
    }

    register() {
        this.container
            .made(DiskManagerInterface,(diskManager) => {
                diskManager.registerForType('s3', (config) => new S3Disk().setBucket(config.bucket));
            });
    }

}
```

## Config custom disk
```js
disk =  {
    default: 'myDisk',
    disks  : {
        myDisk: {
            adapter : 's3', // type for disk
            bucket  : 'nameMyBucket' 
        },
        ...
    },
};
```