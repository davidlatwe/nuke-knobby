<h1 align=center>nuke-knobby</h1>

<p align=center><i>Parsing back and forth between dict and node knobs in Nuke.</i></p>

### Features
* Convert dict data into Nuke node knobs.
* Extract knob values back into dict.

### Example

```python
import nuke
from collections import OrderedDict
from knobby import util

node = nuke.createNode("NoOp")
data = OrderedDict([
    # Regular type of attributes
    ("myList", ["x", "y", "z"]),
    ("myBool", True),
    ("myFloat", 0.1),
    ("myInt", 5),

    # Creating non-default imprint type of knob
    ("MyFilePath", util.Knobby("File_Knob", "/file/path")),
    ("divider", util.Knobby("Text_Knob", "")),

    # Manual nice knob naming
    (("my_knob", "Nice Knob Name"), "some text"),

    # dict type will be created as knob group
    (
        "KnobGroup",
        OrderedDict([
            ("knob1", 5),
            ("knob2", "hello"),
            ("knob3", ["a", "b"]),
        ])
    ),

    # Nested dict will be created as tab group
    (
        "TabGroup",
        OrderedDict([
            ("tab1", {"count": 5}),
            ("tab2", {"isGood": True}),
            ("tab3", {"direction": ["Left", "Right"]}),
        ]),
    ),
])

util.imprint(node, data, tab="Demo")

```

#### Result

![image](https://user-images.githubusercontent.com/3357009/71744866-82f71800-2ea3-11ea-9beb-afe148ac256e.png)
