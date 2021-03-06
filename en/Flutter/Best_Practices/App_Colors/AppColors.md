# Custom App Colors
Custom App Colors are the generic static variables which will store the custom colors. So the system need not to repaint the again and again while using the colors inside application.

## Why to use App colors

-  By determining the app colors generically will reduce the redundancy of the code.

- System does not need to repaint the colors again again inside the application each and everytime while the Color code is used.

- This will increase the performance of the application.

- By using the static constant variable which stores the color will improve the efficiency of application along with an optimized code.

## How to create a custom app colors

- 1. Create a class file as `AppColors`.

- 2. Import `'package:flutter/material.dart'`.  

- 3. Create static variables and assign the colors the format required. For example like, HEX or RGBO.

![Alt text](../App_Colors/images/app_color.png)

## How to use custom app colors

- 1. Import AppColors `package:smart_grinder_mobile/themes/app_colors.dart` inside the file where the color is required.

- 2. Use the color from the AppColor class.


![Alt text](../App_Colors/images/app_color_used.png)

