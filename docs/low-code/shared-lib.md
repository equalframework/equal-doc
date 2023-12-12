# Using `shared-lib` to develop eQual custom app with Angular

When developing custom application in a package of eQual, things can be tough. For example, it can be hard to know where is the eQual root URL or you may want to use the built-in view renderer
of eQual UI.

To make things easier, you can use `sb-shared-lib` an Angular lib that allow you to access eQual the
same way as a native eQual app.


## Compiling `shared-lib`

### Node version

To compile  `sb-shared-lib` you will need to use node 16.

If you need to install it aside of another node installation, you may use [Node Version Manager](https://github.com/nvm-sh/nvm) as follow :
```bash
nvm install 14.18
```
this allow your current directory to be bound to this specifically node version.

### SharedLib

Next, you'll need to clone symbioseUI and build it.

```
git clone https://github.com/yesbabylon/symbiose-ui.git
# you probably need to checkout to dev-2.0 to get newest version of symbioseUI
git checkout dev-2.0
git pull
cd symbiose-ui
npm install
cd sb-shared-lib
ng build
cd dist/sb-shared-lib
npm link 
```

Once you did this you only need to add equal.bundle.js to the library folder you linked

### eQUI (eQual UI)

Finaly, you can clone and build eQualUI

```
git clone https://github.com/cedricfrancoys/equal-ui.git
cd equal-ui
npm install
sh export.sh
```

## Using `shared-lib` in your app

### Linking `sb-shared-lib` in the node_modules of your app

To use sb-shared-lib, you need to link the library to your project.

In the root of your project directory (regardless to your node version) :
```
npm link sb-shared-lib
```

### Using `shared-lib` in Angular

When you need to use the library in a Angular module :
```ts
import { SharedLibModule } from 'sb-shared-lib';

@NgModule({
    declarations: [

    ],
    imports: [
        CommonModule,
        SharedLibModule,
    ],
    exports: [
    ],
    providers: [

    ],
})
export class MyAppModule { }
```

## Code Snippets

### Creating custom API service using `ApiService`

```ts
import { ApiService } from 'sb-shared-lib';


@Injectable({
    providedIn: 'root'
})
export class CustomApiService {
    public cached_schema:any

    constructor(
        private api: ApiService,
    ) {  }
    
    /**
     * Sample function that checks consistency of pkg
     * 
     * @param pkg package to check
     * 
     * @return list of the message about the consistency of the package
     */
    public async getPackageConsistency(pkg:string):Promise<string[]> {
        var ret = []
        try {
            ret = await this.api.fetch('?do=test_package-consistency&package='+pkg);
        }
        catch (response: any) {
            // Create a snack with an error message depending on the context and the HttpError instance
            this.api.errorFeedback(e) 
        }
        return ret;
    }

}
```

### Using `EnvService` to locate eQual endpoints

```ts
import { EnvService } from 'sb-shared-lib';

@Component({
    selector: 'app-controller-info',
    templateUrl: './controller-info.component.html',
    styleUrls: ['./controller-info.component.scss'],
    encapsulation : ViewEncapsulation.Emulated
})
export class ControllerInfoComponent implements OnInit {
    public backend_url:string = "";
    public rest_api_url:string = "";

    constructor(
        private env:EnvService
    ) { }

    async ngOnInit() {
        const env = (await this.env.getEnv())
        this.backend_url = env["backend_url"]
        this.rest_api_url = env["rest_api_url"]
    }
}
```