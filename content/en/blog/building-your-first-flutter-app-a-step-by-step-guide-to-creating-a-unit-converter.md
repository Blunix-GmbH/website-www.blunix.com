---
title: "Step-by-Step Guide to Creating your First Flutter App"
description: "Learn to build your first Flutter app with our step-by-step guide to creating a unit converter application. Begin your mobile app development journey today!"
date: 2024-04-06
image: "/images/blog/flutter-logo.webp"
image_alt: "Step-by-Step Guide to Creating your First Flutter App"
---

Please [contact us](/index.html#contact "Blunix GmbH contact options") if anything is not clearly described, does not work, seems incorrect or if you require support.

## Table of contents

- [Step 1: Creating the new project [¶](#step1)](#step-1-creating-the-new-project-step1)
- [Step 2: Defining the unit data type [¶](#step2)](#step-2-defining-the-unit-data-type-step2)
- [Step 3: Defining units [¶](#step3)](#step-3-defining-units-step3)
- [Step 4: Begin building the GUI [¶](#step4)](#step-4-begin-building-the-gui-step4)
- [Step 5: create the drop-down for selecting the units to convert between [¶](#step5)](#step-5-create-the-drop-down-for-selecting-the-units-to-convert-between-step5)
- [Step 6: create text-field for inputting the unit quantities [¶](#step6)](#step-6-create-text-field-for-inputting-the-unit-quantities-step6)
- [Step 7: putting everything together [¶](#step7)](#step-7-putting-everything-together-step7)

## Step 1: Creating the new project [¶](#step1)

First lets install flutter:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">sudo snap install flutter --classic</code></pre>

To verify that flutter is working correctly:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">flutter doctor</code></pre>

Begin by creating a new Flutter project. Open your terminal and run:

<pre class="command-line language-bash" data-output="2-99" data-continuation-str="\" data-prompt="user@workstation:~$">
<code class="language-bash">flutter create unit_converter</code></pre>

This command will create the default flutter app.

## Step 2: Defining the unit data type [¶](#step2)

First create a new file titled unit.dart in the lib directory in the project.

In the file start by defining an enumeration for unit types:

```flutter
enum Type { weight, length, time }
```

Then, introduce a class to represent various units, incorporating a name, type (from the enum), and a value relative to standard SI units:

```flutter
class Unit {
  String name;
  Type type;
  double valSiUnits;

  Unit(this.name, this.valSiUnits, this.type);
}
```

For unit conversion functionality, add:

```flutter
  double? convert(double? val, Unit to) {
    if (type != to.type) {
      throw Exception("Trying to convert between incompatible types");
    }
    if (val == null) return null;
    return val * valSiUnits / to.valSiUnits;
  }
```

This method checks that the units are compatible (i.e., of the same type), handles null inputs gracefully by returning null if the input value is null, and performs the conversion using the ratio of the units' SI values.

## Step 3: Defining units [¶](#step3)

Since the physical constants we're dealing with are immutable and our application's scope is defined, we can define our units as global constants within unit.dart:

```flutter
final Map> units = {
  Type.weight: [
    Unit("Kilogram", 1.0, Type.weight), // SI unit
    Unit("Gram", 0.001, Type.weight),
    Unit("Pound", 0.453592, Type.weight),
    Unit("Stone", 6.35029, Type.weight),
  ],
  Type.length: [
    Unit("Meter", 1.0, Type.length), // SI unit
    Unit("Centimeter", 0.01, Type.length),
    Unit("Inch", 0.0254, Type.length),
    Unit("Mile", 1609.34, Type.length),
    Unit("Kilometer", 1000, Type.length),
  ],
  Type.time: [
    Unit("Second", 1.0, Type.time), // SI unit
    Unit("Minute", 60.0, Type.time),
    Unit("Hour", 3600.0, Type.time),
    Unit("Day", 3600 * 24, Type.time),
    Unit("Week", 3600 * 24 * 7, Type.time)
  ],
};
```

You can easily expand this by adding new units and unit types.

## Step 4: Begin building the GUI [¶](#step4)

Navigate back to the main.dart file to start building the user interface.

Ensure unit.dart is imported:

```flutter
import 'package:unit_converter/unit.dart';
```

Edit the build function of the MyApp class to customize the app's theme and title. This is where you set the global appearance of your app and its title as displayed in the task switcher.

```flutter
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Unit Converter',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.lightGreen),
        useMaterial3: true,
      ),
      home: const MyHomePage(title: 'Unit Converter'),
    );
  }
```

You can tinker with this to better see how it changes the app.

In \_MyHomePageState, introduce variables to represent the conversion logic, including the units and values involved, and controllers for text input.

```flutter
  Type unitType = Type.weight;
  Unit fromUnit = units[Type.weight]![0];
  Unit toUnit = units[Type.weight]![1];
  double? valueFrom = 0;
  double? valueTo = 0;
  final controllerFrom = TextEditingController();
  final controllerTo = TextEditingController();
```

We need to provide users with the ability to select unit types. One way to achieve this is by using a PopupMenuButton in the Appbar. Here is a straightforward implementation of it:

```flutter
      appBar: AppBar(
        backgroundColor: Theme.of(context).colorScheme.inversePrimary,
        title: Text(widget.title),
        actions: [
          PopupMenuButton(
            itemBuilder: (BuildContext context) {
              return [
                PopupMenuItem(
                  onTap: () {
                    unitType = Type.weight;
                  },
                  child: const Text("Weight"),
                ),
                PopupMenuItem(
                  onTap: () {
                    unitType = Type.weight;
                  },
                  child: const Text("Length"),
                ),
                PopupMenuItem(
                  onTap: () {
                    unitType = Type.time;
                  },
                  child: const Text("Time"),
                )
              ];
            },
          ),
        ],
      ),
```

While this method works well for our small app, to enhance maintainability and scalability, especially as the development progresses and the app grows, it's beneficial to automate the creation of popup menu items. This avoids manual item declaration and makes adding new unit types much simpler:

```flutter
              List items = [];
              for (final type in Type.values) {
                items.add(PopupMenuItem(
                  onTap: () {
                    if (unitType == type) return;
                    unitType = type;
                    fromUnit = units[type]![0];
                    toUnit = units[type]![1];
                    setVal(valueFrom, from: true);
                  },
                  child: Text(type.name),
                ));
              }
              return items;
```

By iterating over Type.values, this approach dynamically generates menu items for each unit type.

The importance of this approach is demonstrated as we update the onTap function to adjust the selected units to match the unit type. With the original method this would have introduced excessive redundancy.

During debugging, be aware that the debug banner may obscure the menu icon. This experience emphasizes the importance of testing your UI in both development and production modes to ensure usability.

## Step 5: create the drop-down for selecting the units to convert between [¶](#step5)

While building a unit converter app, it's essential for the text fields to update automatically with every change of the unit quantity or the selected units. This requires a function specifically for managing these updates, making the app more responsive and user-friendly.

```flutter
  void setVal(double? val, {required bool from, bool modifyChanged = true}) {
    if (from) {
      valueFrom = val;
      valueTo = fromUnit.convert(val, toUnit);
    } else {
      valueTo = val;
      valueFrom = toUnit.convert(val, fromUnit);
    }

    if (modifyChanged || !from) {
      controllerFrom.text =
          (valueFrom == null) ? "" : valueFrom!.toStringAsFixed(4);
    }
    if (modifyChanged || from) {
      controllerTo.text = (valueTo == null) ? "" : valueTo!.toStringAsFixed(4);
    }
    setState(() {});
  }
```

This function processes the new unit quantity and a boolean indicating whether we're changing the 'from' or 'to' value. It then updates both the conversion values and their corresponding text fields accordingly.

To implement the unit selection in our converter app, we can use drop-down menus for users to choose the units they're converting to and from. Leveraging the DropdownSearch widget offers a refined user experience with searchable and selectable unit options. Before incorporating DropdownSearch, ensure it's added to your project by running:

```flutter
flutter pub add dropdown_search
```

and importing it in your Dart file:

```flutter
import 'package:dropdown_search/dropdown_search.dart';
```

To avoid redundancy we craft a function, getUnitSelectorWidget, to dynamically generate these drop-down menus. This function is designed to be adaptable, serving both "from" and "to" unit selection scenarios based on a boolean parameter. The implementation is as follows:

```flutter
  Widget getUnitSelectorWidget({required bool from}) {
    return DropdownSearch(
      filterFn: (item, filter) {
        return item.name.toLowerCase().startsWith(filter.toLowerCase());
      },
      compareFn: (item1, item2) {
        return item1.name == item2.name;
      },
      popupProps: const PopupPropsMultiSelection.modalBottomSheet(
        searchDelay: Duration.zero,
        showSelectedItems: true,
        showSearchBox: true,
      ),
      onChanged: (var newValue) {
        if (newValue == null) return;
        if (from) {
          fromUnit = newValue;
        } else {
          toUnit = newValue;
        }
        // Update the converted values by calling setVal
        setVal(valueFrom, from: true);
      },
      selectedItem: from ? fromUnit : toUnit,
      items: units[unitType]!,
      itemAsString: (item) => item.name,
    );
  }
```

In this setup, filterFn and compareFn ensure that units are easily found and correctly matched during searches. popupProps customize the drop-down's interactive features, such as search delay and display settings. Lastly, the onChanged callback updates the app state based on user selection, maintaining real-time responsiveness. Through this approach, we establish a user-friendly interface for selecting conversion units, enhancing the overall functionality of our app.

## Step 6: create text-field for inputting the unit quantities [¶](#step6)

Creating a responsive and user-friendly interface for our unit converter app involves allowing users to input quantities for conversion. This step introduces a function to streamline the creation of input fields, making the app's design cohesive and removing the redundancy that would appear from separately defining the "from" and "to" text fields.

```flutter
  Widget getQuantityInputWidget({required bool from, double width = 200}) {
    return SizedBox(
      width: width,
      child: TextField(
        keyboardType: TextInputType.number,
        controller: from ? controllerFrom : controllerTo,
        onChanged: (value) {
          double? valueNum = double.tryParse(value);
          if (valueNum == null) {
            setVal(null, from: from);
          }
          setVal(valueNum, from: from, modifyChanged: false);
          setState(() {});
        },
      ),
    );
  }
```

In this setup, we configure a text field to accept only numerical values and link it to the correct controller. Then we ensure that when a new value is entered, it's automatically converted, and the corresponding value in the other text field is updated. To prevent the currently active text field from being reformatted while the user is typing, we use the modifyChanged: false parameter.

## Step 7: putting everything together [¶](#step7)

With all the essential components created, it's time to assemble our unit converter app within the build function. By organizing our widgets effectively, we create a user-friendly interface. Here's how the layout can be structured inside the Scaffold’s body:

```flutter
Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Row(
              children: [
                const Spacer(),
                Expanded(child: getUnitSelectorWidget(from: true)),
                const SizedBox(width: 20),
                Expanded(child: getUnitSelectorWidget(from: false)),
                const Spacer(),
              ],
            ),
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: [
                getQuantityInputWidget(from: true),
                const SizedBox(width: 20),
                getQuantityInputWidget(from: false),
              ],
            ),
          ],
        ),
      ),
```

This layout utilizes a Center widget to align the main column in the middle of the screen. We introduce two rows within this column: the first row hosts our unit selection dropdowns, and the second row contains the quantity input fields. Spacers and sized boxes are strategically placed to ensure there's adequate spacing, making the interface aesthetically pleasing and easy to navigate.

With this setup, we've successfully created a simple unit converter app that efficiently handles unit conversions across various dimensions like mass, length, and time, blending functionality with ease of use.
