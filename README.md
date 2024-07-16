ComfyUI_TaterTweaks (forked from https://github.com/comfyanonymous/ComfyUI : if you found this place, just go there instead. This contains just my personal tweaks to make Comfy do what I want. At the moment there's nearly no differences.
=======

Current changes: Modify widgets.js to allow for on-generation incrementation of different amounts. Widgets now have the options increment, increment x5, increment x10, increment x20, increment x25, increment x50, as well as decrement, decrement x5 ... decrement x50.  This allows me to use basic floats with precision up to 2 decimal places, along with regular primitive nodes, to finely step-control things like cfg, loraweights, etc.

## explanation of changes made
Modified ```web/scripts/widgets.js``` to allow for more on-generation control options.

in the file at line 74, change:
```javascript
			values: ["fixed", "increment", "decrement", "randomize"],
```
so it reads:
```javascript
			values: ["fixed", "increment", "increment x5", "increment x10", "increment x20", "increment x25", "increment x50", "decrement", "decrement x5", "decrement x10", "decrement x20", "decrement x25", "decrement x50", "randomize"],
```

Then you have to add the switch cases.
At line 130 you will find this switch:
```javascript
			switch (v) {
				case "increment":
					current_index += 1;
					break;
				case "increment-wrap":
					current_index += 1;
					if ( current_index >= current_length ) {
					    current_index = 0;
					}
					break;
				case "decrement":
					current_index -= 1;
					break;
				case "randomize":
					current_index = Math.floor(Math.random() * current_length);
				default:
					break;
			}
```
You want to change it so it reads:
and
```javascript
			switch (v) {
				case "increment":
					current_index += 1;
					break;
				case "increment x5":
					current_index += 5;
					break;
				case "increment x10":
					current_index += 10;
					break;
				case "increment x20":
					current_index += 20;
					break;
				case "increment x25":
					current_index += 25;
					break;
				case "increment x50":
					current_index += 50;
					break;
				case "increment-wrap":
					current_index += 1;
					if ( current_index >= current_length ) {
					    current_index = 0;
					}
					break;
				case "decrement":
					current_index -= 1;
					break;
				case "decrement x5":
					current_index -= 5;
					break;
				case "decrement x10":
					current_index -= 10;
					break;
				case "decrement x20":
					current_index -= 20;
					break;
				case "decrement x25":
					current_index -= 25;
					break;
				case "decrement x50":
					current_index -= 50;
					break;
				case "randomize":
					current_index = Math.floor(Math.random() * current_length);
				default:
					break;
			}
```

You'll then find another switch at line 165 in the original file (now line 195):
```javascript
//adjust values based on valueControl Behaviour
			switch (v) {
				case "fixed":
					break;
				case "increment":
					targetWidget.value += targetWidget.options.step / 10;
					break;
				case "decrement":
					targetWidget.value -= targetWidget.options.step / 10;
					break;
				case "randomize":
					targetWidget.value = Math.floor(Math.random() * range) * (targetWidget.options.step / 10) + min;
				default:
					break;
			}
```
Change it so that it reads:
```javascript
			switch (v) {
				case "fixed":
					break;
				case "increment":
					targetWidget.value += targetWidget.options.step / 10;
					break;
				case "increment x5":
					targetWidget.value += targetWidget.options.step / 2;
					break;
				case "increment x10":
					targetWidget.value += targetWidget.options.step;
					break;
				case "increment x20":
					targetWidget.value += targetWidget.options.step * 2;
					break;
				case "increment x25":
					targetWidget.value += targetWidget.options.step * 2.5;
					break;
				case "increment x50":
					targetWidget.value += targetWidget.options.step * 5;
					break;
				case "decrement":
					targetWidget.value -= targetWidget.options.step / 10;
					break;
				case "decrement x5":
					targetWidget.value -= targetWidget.options.step / 2;
					break;
				case "decrement x10":
					targetWidget.value -= targetWidget.options.step;
					break;
				case "decrement x20":
					targetWidget.value -= targetWidget.options.step * 2;
					break;
				case "decrement x25":
					targetWidget.value -= targetWidget.options.step * 2.5;
					break;
				case "decrement x50":
					targetWidget.value -= targetWidget.options.step * 5;
					break;
				case "randomize":
					targetWidget.value = Math.floor(Math.random() * range) * (targetWidget.options.step / 10) + min;
				default:
					break;
			}
```

This just gives you these new incrementation choices and incorporates the necessary logic. I like it to change the input of the lora model_weight into an input, so i can just use a primitive node and increment those on generation. this lets me get intermediate weight tests done easily and efficiently to find the weights at which a lora (or anything you can control with a primitive float) works best. Modification is intuitive and simple. If you need this, have fun with it.
