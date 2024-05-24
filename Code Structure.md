| PATH                                | PURPOSE                                                                                               |
| ----------------------------------- | ----------------------------------------------------------------------------------------------------- |
| src/{Namespace}/                    | Use this directory for developing. All the code for Yves and Zed is located here.                     |
| vendor/spryker, vendor/spryker-shop | Contains the code of the Spryker-core. It follows the architectural rules used in the project’s code. |
| vendor/{vendor}/{package}/          | All the other packages that are installed using composer install.                                     |
| data/                               | Log files and other temporary data.                                                                   |
| public/Yves/index.php               | Web-server entry point of the Storefront application.                                                 |
| public/Yves/assets/                 | Static files, such as CSS, JS, and assets, for the project’s Yves.                                    |
| public/Zed/index.php                | Web-server entry point of the Back Office application.                                                |
| public/Zed/assets/                  | Static files, such as CSS, JS, and assets, for the project’s Zed.                                     |
| public/Glue/index.php               | Web-server entry point of the Storefront API application.                                             |