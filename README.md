This project demonstrates a Micro Frontend Architecture using Angular and Webpack Module Federation. It consists of two separate applications:

Dashboard Shell (Host Application): Serves as the main container.

Feature Module (Remote Application): A micro frontend providing a standalone feature.

The applications run independently but integrate seamlessly via dynamic module loading.


Project Setup & Installation

1. Install Angular CLI

Ensure Angular CLI is installed globally:

npm install -g @angular/cli

2. Create the Applications

ng new dashboard-shell --routing --style=scss
ng new feature-module --routing --style=scss

Navigate into each folder and install dependencies:

cd dashboard-shell
ng add @angular-architects/module-federation
cd ../feature-module
ng add @angular-architects/module-federation



Configuring Webpack Module Federation

1.Dashboard Shell (Host)

Modify webpack.config.js:

const { ModuleFederationPlugin } = require("webpack").container;
const mf = require("@angular-architects/module-federation/webpack");

module.exports = mf({
  name: "dashboard_shell",
  remotes: {
    featureModule: "featureModule@http://localhost:4201/remoteEntry.js",
  },
  shared: ["@angular/core", "@angular/common", "@angular/router"]
});

Modify app-routing.module.ts:

const routes: Routes = [
  { path: 'feature', loadChildren: () => import('featureModule/FeatureModule').then(m => m.FeatureModule) }
];

2.Feature Module (Remote)

Modify webpack.config.js:

module.exports = mf({
  name: "featureModule",
  filename: "remoteEntry.js",
  exposes: {
    './FeatureModule': './src/app/feature/feature.module.ts',
  },
  shared: ["@angular/core", "@angular/common", "@angular/router"]
});

Create feature.module.ts:

@NgModule({
  declarations: [FeatureComponent],
  imports: [
    CommonModule,
    RouterModule.forChild([{ path: '', component: FeatureComponent }])
  ]
})
export class FeatureModule {}


Styling with Tailwind CSS Using CDN : <script src="https://cdn.tailwindcss.com"></script>

Run the Applications

Run the Feature Module (Remote) first:ng serve --port 4201
Run the Dashboard Shell (Host):ng serve --port 4200


