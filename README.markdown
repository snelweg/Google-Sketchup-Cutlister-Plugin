# README for Cutlister

[Cutlister][] is a Google SketchUp Plugin that automates the creating of cut lists for your woodworking project. A cut list is a table of parts used to make up a piece of cabinetry or furniture (such as a door, wall end, back or finished end).

This plugin also exports layouts for your sheet and part goods, allowing you to visualize exactly how much material you will need and how to cut it out, thus saving you material on your next project (*Layouts are currently not implemented!*).

This plugin is specifically designed for cabinet makers and woodworkers, but could also apply to other industries and applications.

Cutlister takes all the selected entities in your model and creates a cut list for those parts

* **Sheet goods** -- Any sheet good (e.g. plywood, MDF, etc...)
* **Solid Stock** -- Anything cut out of lumber.
* **Hardware** -- Anything else (e.g. Accessories, appliances, etc...)

The plugin exports these items in a variety of lists types and formats.

You can choose from outputting an HTML page, a CSV file (for importing into programs like Microsoft Office and Apple iWork Numbers), printing labels (only Avery 5366 labels currently) and, eventually, other formats. If there is a format you are interested in using, please fork the [Github repository][Cutlister] or create a case on the [GitHub issue tracker][githubrepo] to add it in. There is no guarantee that I will get around to it, but if I think it is a good idea, I may end up doing it.

This plugin was designed to be extendable, allowing you to add your own output formats, cut lists and renderers. This means you could generate custom cut lists for your own particular needs without changing anything in the current code (more on this below).

## Extra stuff

http://rubydoc.info/github/snelweg/Google-Sketchup-Cutlister-Plugin/master/frames/toplevel


## Installation

Copy the file `Cutlister.rb` file and the `Cutlister` directory into your SketchUp plugin directory:

On a **Mac** it is usually here:

    Macintosh HD/Library/Application Support/Google SketchUp X/SketchUp/

On **Windows** it should be here:

    C:\Program Files\Google\Google Sketchup X\Plugins\

.. where `X` is the version of SketchUp you are running.

Once the plugin is copied over, restart SketchUp and the plugin should be working.

You will see a right-click context menu item called "Cutlist selection" as well as a menu entry under "Plugins" named "Cutlist model".


## Usage

To use Cutlister, just select the items in your model you want to cut list and right-click (context click) on them and select "Cutlist Selection", or use the item in the "Plugins" menu, or the Toolbar item.

In order to get Cutlister to work right in your models, you may need to make some changes to your workflow. Below is the general work flow for getting Cutlister to work as expected:

- Layers are used to group similar parts to make it easier to isolate them and make changes (e.g. "Doors", "Shelfs", "Counters", etc...). *It has nothing to do with the cut listing of parts*.

- Group each individual part to isolate it's geometry. Give the part a name (e.g. "Wall End", "Door", "Back", etc...) which will be used within Cutlister as the *Part Name* in Cutlister.

- Group collections of parts into sub-assemblies (e.g. Cabinet numbers, like "101 1", "204 7", "Kitchen Vanity", etc...) which will be used as the cabinet number in the cut listing program. *You can use any naming convention that you like*, you are not limited to numbering your cabinets in any particular way. This will be used as the *Cabinet Name* in Cutlister.

- Give the individual parts a material fill (e.g. "PF-MAPLE", "MDF", "Poplar". etc...) which will be *used as the material in Cutlister*. Do not apply a material to a group of components because the properties will not work in Cutlister (e.g. don't give the cabinet a material but give the parts that make up the cabinet a material). *Material numbers can be have letters, numbers, spaces, dashes (-), underscores (_) and be upper or lowercase*.

Once your models is setup, you can begin to cut list.

Once you have the Cutlister popup menu open, set your options and then click "Export" to generate your cut list.

You can choose the **Format** of the export, which will output the cut list into different formats. Currently there are there choices:

* **Web Page** -- Opens a web browser window for printing.
* **CSV** -- Creates a `.csv` file in the same directory as your drawing, which you can then use in Excel or another similar program.
* **Label** -- Which opens a web browser window for printing labels for your parts. Note that you must select the *List Type* "Label (Avery 5366)" for this to work right (or create your own label List Type).

Next, you can select the **List Type** you want to export which controls how the data is structured in the cut list:

* **Batched** -- Outputs a cut list of the parts grouped first by *Sheet Goods*/*Solid Stock*/*Hardware*, then within those sections it is grouped by *Material*, then finally by the *Thickness*. This is generally used for batched cutting of parts for cabinets.
* **Individual** -- Outputs the the cut list based on the *Cabinet Name* (aka the Sub Assembly). This is generally used when assembling cabinets.
* **Full** -- This exports everything, ungrouped. This is rarely used but provided in case you just want all the parts in one big list.
* **Label (Avery 5366)** -- Outputs parts onto sticky labels (that fit on an Avery 5366 template sheet). Note that you must select the *Label* format from the "Formats" select box for this to work.

You can choose to **Show** only *Sheet Goods*, *Solid Stock*, *Hardware* or any combination of them.

The **Sheet Goods** list is a comma separated list of material names that match materials in your model. If the material name is in this list, it will be grouped as a *Sheet Good* part. The field is case-insensitive, meaning if you input "mdf", it would match any material in your model named "mdf", "MDF", "mDf", or any combination of lowercase and capital letters. This field also allows you to have materials that contain spaces, dashes and underscores (e.g. "Shop Ply", "Pre-finish Maple", "peruvian_walnut", etc...).

The **Solid Stock** list is the same as the *Sheet Goods* list above, but it instead labels parts as *Solid Stock*. The same rules apply for items in the list. Generally, solid stock is considered to be anything cut out of lumber.

## Extending Cutlister

Cutlister is designed to be extensible. This means that you can create new renderers, output formats and cut lists based on your needs. 

If you wanted to export an XML file, for example, you could create a sub-class of the `Renderer`class (which is found in `renderers.rb`)  to construct your XML file and then create a sub-class of the `Cutlist` class (in `cutlist.rb`) to format the cut list output to work with XML.

- **Renderers** format the content for a particular output like HTML, CSV or something else. Currently there are a base class called `Renderer` which is just a skeleton for other sub-classes, `HTMLRenderer` which constructs HTML tables and headings for a cutlist, and `CSVRenderer` which constructs cut lists for usage in programs that support `.csv` files (e.g. Excel, Numbers, Open Office, etc...). More renderers may be added in the future.
- **Cutlists** create the layout of a given cut list, what is displayed and in which order or grouping. You can sub-class the `Cutlist` class to create new cut list layouts based on your needs. Currently there are "Batched", "Individual" and "Full" cut lists (which are explained above). You could create a cut list that formats your list into groupings based on any one of the many properties, such as width, thickness, length, material, sub_assembly, part name, etc...

Once you sub-class `Renderer` or `Cutlist`, your custom renderer/cutlist will show up in the "Format" and "List Type" select boxes in the UI once you restart SketchUp.

See the file `Cutlister/extensions/` folder for an example of how to do this.


## Credits

Cutlister was inspired by the [CutList][] project by: 

- [Steve Racz](http://steveracz.com/)
- Dave Richards

It was completely re-written from the ground up and uses only a few small parts of the original code.

A few differences of Cutlister from [CutList][] are as follows:

- **More Easily Extendable** -- Adding new output formats or cut list types are much more streamlined, with automatic sub-class "sniffing", which means that if you sub-class `Renderer` or `Cutlist` the plug-in automatically adds the new sub-class to the list of formats and list types. To add a new output format or cut list all you need to do is create a sub-class of the parent class and define how it should work, everything else is taken care of.

    See the `Cutlister/extension/` folder for an example of how to do this. When adding a new extension, make sure to load them in the `Cutlister.rb` file and restart SketchUp so they work.
- **Improved User Interface** -- The design for the plugin was cleaned up and improved to be easier to use and more attractive as well as more easily modifiable. The interface has automatic notification messages built in, rather than the somewhat annoying pop-up messages.
- **Version Controlled and Open Source** -- The plugin is [hosted on Github](https://github.com/danawoodman/Google-Sketchup-Cutlister-Plugin) and is released under a MIT license (see the `LICENSE` file for more information on the license). This means you can fork it, modify the code and use it in any manner you please.
- **Lots of Code Comments** -- Where possible I provided comments on the code as to what is going on so modification and understanding of the code is improved. I will also make my best attempt at keeping a good tutorial and reference page maintained so extending this plug-in will be as straight forward as possible.


## Notes & Caveats

This plugin is currently under heavy development and some parts are not functional yet.

One major note is that **Cutlister does not do "layouts"** currently. The original [CutList][] has support for something called "layout" which allows you to output your cut list as a printable panel layout that you can use to visualize how your parts will fit on a sheet of plywood or other material.

Since Cutlister was completely re-written, it must be built from the ground up, and the layout feature has not yet been integrated.

I am planning on implementing the layout features as soon as the cut listing parts are stable and I have determined the best way to implement the feature so that it is flexible for multiple uses.


## Contributing, Feedback & Bug Reports

If you want to contribute to this project, please fork Cutlister at the [Github repository][Cutlister] and send me pull requests if you want to see your change integrated.

If you have any feedback or comments, feel free to send me an email at <dana@danawoodman.com>.

If you find bugs or have a feature request, please add them at the [GitHub issue tracker][githubrepo].

## License

Cutlister is licensed under an MIT license. See the `LICENSE` file for more information.


[Cutlister]: https://github.com/danawoodman/Google-Sketchup-Cutlister-Plugin "Visit the Cutlister GitHub page"
[CutList]: http://steveracz.com/joomla/content/view/45/1/ "CutList by Steve Racz"
[githubrepo]: https://github.com/danawoodman/Google-Sketchup-Cutlister-Plugin/issues "Add any bugs or feature request to the Issues page"