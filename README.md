# Color Mapping

## Dataset Description:

- The color mapping and contour plot datasets follow the following pattern:
- The First Six lines of the dataset provide some information about the dataset such as the variable being plotted (surface rain rate in our case), the Flag which denotes a bad value (-999 in this dataset), the number of rows and columns in the dataset and the date on which the data was collected.
- From the 7th line, the data is presented. The dataset follows the following pattern:
- Each column corresponds to a longitude. The longitudes begin at 0.125E.
- Each row corresponds to a latitude. The latitudes begin at 89.875S and ends at 89.875N.
- Each cell corresponding to a column (longitude) and a row (latitude) contains the sea surface rain rate at that longitude and latitude.
- Our group had chosen April - May 2016 as the months for performing the Color Mappings. Hence, we chose 10 dates: 
    - 1, 10, 20, 30 April 2016
    - 10, 20, 30 April 2016
    - 10, 20, 30 June 2016 

## Color Mapping Data Processing: 

- We ignore the first 7 lines and start reading the text file from the 8th line. We ignore the 7th line as well since the longitudes in the dataset are in the same order that the library expects. Hence, we do not need to explicitly store the longitudes to perform any sort of data processing later on.
- We store each line in a list. We drop the first element of each list, which corresponds to the latitude for that line. The reason for dropping the latitude is the same as the reason for dropping the longitudes. 
- Due to the limitation of ASCII Text files, certain rows contain the message ** line too long ** at the end of the line.
- We split the line at all the spaces. If the last element of the line contains ** line too long **, we pop the last element out of the list. We then convert all strings (the surface rain rates) to their float values. 
- Due to the limitation of Text files, not all rows contain 1440 values. Hence, we keep adding the BAD VALUE to the list until the length of the list is 1440. 
- All these lists (rows) are added to another list to generate a 2D list.

## Color Mapping Implementation

- The *generate_dataset(name)* is a function that takes in a name of the text file (dataset), processes it and returns a 2D List, as described in the Data Processing section.

- The *continuous_plot(data,title,cmap)* performs a continuous color mapping of the data on the world map. The implementation of the method is as follows:

    - Input Parameters:
        - data: A 2D NumPy array or similar data structure containing the values to be plotted.
        - title: A string specifying the title of the plot.
        - cmap: A colormap object that determines the color mapping of the data values.

    - Plot Initialization:
        - The function starts by creating a new figure and axis using Matplotlib[^1] (fig, ax = plt.subplots(...)).
        - It specifies a Plate Carrée projection for the axis, which is commonly used for world maps, with the central longitude set to 180 degrees.

    - Plot Data: The data is plotted on the map using the imshow function with the following parameters:
        - data: The 2D array to be plotted.
        - origin='lower': Specifies that the data's origin is in the lower-left corner.
        - cmap: The colormap used for color mapping.
        - extent: The spatial extent of the data, specified as (-180, 180, -90, 90), covering the entire world.
        - transform: The transformation to Plate Carrée projection with the central longitude set to 180.
        - vmin and vmax: The minimum and maximum values for color scaling.

    - Map Features: The function adds coastlines to the map using ax.add_feature(cfeature.COASTLINE) to provide geographic context.

    - Colorbar: A colorbar is added to the plot using plt.colorbar(...). It displays the color mapping of the data with the specified title.

    - Axis Labels and Title: The function sets labels for the X and Y axes, as well as a title for the plot using ax.set_xlabel(...), ax.set_ylabel(...), and ax.set_title(...).

    - Map Extent: We set the map's extent to cover the world from 0&deg; Longitude to 300&deg; Longitude and -90&deg; Latitude to +90&deg; Latitude with ax.set_extent([0, 300, -90, 90], ccrs.PlateCarree()).

    - Display the Plot: Finally, the function displays the plot using plt.show().

- The *discrete_plot* function is similar to the previously described *continuous_plot* function, but with a focus on visualizing discrete data rather than continuous data. It plots data on a world map using Matplotlib and Cartopy, with discrete color mapping based on specified boundaries and colors. Here's what is different in the implementation of discrete mapping:

    - Boundaries and Colors:
        - The boundaries list defines the boundaries that separate the discrete intervals for data visualization. These boundaries determine the range for each color category. We set the values to between 0 and 0.5, with a step of 0.05.
        - The colors list defines a corresponding set of colors for each category, with a specific color assigned to each range between the boundaries. The list of colors should be in the same order as the boundaries, with colors assigned to categories from low to high values.

    - BoundaryNorm: 
        - The BoundaryNorm is an essential component for discrete color mapping. It is created with the boundaries and the number of colors to be used (len(colors) in this case).
        - The BoundaryNorm is used to map data values to the appropriate color category based on the specified boundaries. It ensures that data falling within a specific range is assigned the corresponding color from the colors list. 

For the purpose of the discrete color mapping, I have chosen discrete colors that correspond to the viridis color mapping, which can be generated here[^2].

- The *log_plot* function is another function for visualizing data on a world map using Matplotlib and Cartopy, but it differs from the previous functions in how it handles data scaling and color mapping. Here's a description of what is different in this function:

    - Scaling with Logarithmic Normalization: The primary difference in this function is the use of logarithmic normalization for scaling the data. This is achieved by creating a LogNorm instance and assigning it to the norm variable.

    - Color Map: In this function, the color map used is "viridis." Viridis is a perceptually uniform color map, designed to work well with people who have color vision deficiencies. It's especially suited for data visualizations where the relative ordering of data values is important.

## Implementation

- In order to try different color maps, only the first dataset (of 1st April 2016) is chosen. 
- The dataset is fed to the *data = numpy.ma.masked_where(data == BAD_VALUE, data)* masks the BAD_VALUE and is not plotted on the world map.
- Then, to decide the best color map, I followed the following steps and performed the color mapping on all the 10 dates chosen:
    - First, I performed a continous plot with different color mappings[^3]. Refer to the report to view the different experimented color mappings and to find out why I chose viridis.
    - Next, I performed a continous, discrete and logarithmic color mapping. Refer to the report for why I chose continous color mapping.
- Finally, an .mp4 animation was created using the 10 different continous color maps, whose text files are in the datasets folder. The implementation for generating the animation is:

    - Update Function:
        - The update function is a callback function invoked for each frame of the animation. It receives the frame argument, representing the current frame number.
        - Within the update function, the cax (image plot) is updated with the data from the data_list for the corresponding frame and the title is also changed respectively.

    - Frame Generation:
        - The frame generation process involves creating individual frames that constitute the animation. An empty list called frames is initiated to store these frames.
        - A loop iterates through each item in the data_list. For each item (frame), it calls the continuous_plot function, passing the data and title as input.
        - Each call to the continuous_plot function generates a single frame, effectively capturing a snapshot of the data for that frame.
        - These frames are sequentially added to the frames list. As the loop progresses, more frames are collected until all frames have been generated. The frames list contains all the frames necessary for the animation.

    - Animation Creation:
        - The animation creation phase is responsible for assembling the frames into a coherent animation. This is achieved using the FuncAnimation class.
        - The class takes several parameters, including the figure, the update function (update), the number of frames (equal to the length of the data_list), and other animation settings.
        - In this case, the animation is configured not to repeat after playing once (repeat=False). Additionally, there's a repeat delay of 3000 milliseconds before the animation restarts.
        - The final result is an animation represented by the ani variable, which combines all the frames and is ready for saving as a video. An fps rate of 1 is specified, which means each visualization appears 1 second after the previous one.

[^1]: https://matplotlib.org/stable/ 
[^2]: https://waldyrious.net/viridis-palette-generator/ 
[^3]: https://matplotlib.org/stable/users/explain/colors/colormaps.html