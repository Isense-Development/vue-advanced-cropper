---
title: Recipes
---

# Recipes

## Changing a stencil

There are only two default stencil components now, `RectangleStencil` (default) and `CircleStencil`, but you can easily create your own stencil himself.

To specify stencil component you should pass it to `stencilComponent` prop. For globally registered component just pass their name:
```html
<cropper
	stencil-component="circle-stencil"
/>
```

But if your component is not registered globally you should pass the component’s options object.

There is one of approaches to pass it:
```js
import { CircleStencil, Cropper } from 'vue-advanced-cropper';

export default {
	components: {
		Cropper, CircleStencil
	},
	data() {
		return {
			img: 'https://images.unsplash.com/photo-1485178575877-1a13bf489dfe?ixlib=rb-1.2.1&auto=format&fit=crop&w=991&q=80',
		};
	},
};
```

```html
<div id="app">
  <cropper
	src="https://images.pexels.com/photos/1254140/pexels-photo-1254140.jpeg?auto=compress&cs=tinysrgb&dpr=2&h=650&w=940"
	:stencil-component="$options.components.CircleStencil"
  />
</div>
```

The cause of this specificity is using the [dynamic components](https://vuejs.org/v2/guide/components.html#Dynamic-Components) approach to allow use an arbitrary stencil component.

<circle-example> </circle-example>

## Passing props to the stencil

To pass any props to the stencil pass them as object to `stencilProps` prop. 

For example, that's how you can set aspect ratio:

```html
<cropper
	:stencil-props="{
		aspectRatio: 6/9,
		movable: false,
		resizable: false
	}"
/>
```

The list of available props varies from one stencil component to another. The props of default stencils are available at this site ([RectangleStencil](/components/rectangle-stencil.html), [CircleStencil](/components/circle-stencil.html))

## Setting the aspect ratio

This library supports setting either aspect ratio value or aspect ratio range (i.e. minimum and maximums aspect ratio values).

Generally speaking, aspect ratio is the property of the stencil, not the cropper, so the possibility to set aspect ratio
entirely depends of used stencil. For example, `CircleStencil` aspect ratio can't be customized, it's always circle, in the same time
`RectangleStencil` has not any restrictions on aspect ratios.

The examples below are written for `RectangleStencil`. 

### Fixed aspect ratio
```html
<cropper
	:stencil-props="{
		aspectRatio: 1/1,
	}"
/>
```

### Aspect ratio range
```html
<cropper
	:stencil-props="{
		minAspectRatio: 16/8,
		maxAspectRatio: 4/8
	}"
/>
```

## Getting the result

### First method

You can get the coordinates of stencil and canvas with cropped image by processing `change` event.

::: tip
Cropper will emit `change` event on clear or load (include initial) image, on resize and move the stencil, on resize, move, rotate and flip the image.
:::

<getting-result-example></getting-result-example>

```js
import { Cropper } from 'vue-advanced-cropper';

export default {
	components: {
		Cropper,
	},
	data() {
		return {
			coordinates: {
				width: 0,
				height: 0,
				left: 0,
				top: 0,
			},
			image: null,
		};
	},
	methods: {
		onChange({ coordinates, canvas, }) {
			this.coordinates = coordinates;
			// You able to do different manipulations at a canvas
			// but there we just get a cropped image, that can be used 
			// as src for <img/> to preview result
			this.image = canvas.toDataURL();
		},
	},
};
```

```html
<div id="app">
	<cropper
		src="https://images.pexels.com/photos/226746/pexels-photo-226746.jpeg"
		@change="onChange"
	/>
</div>
```

### Second method

Also there is alternative to get the cropper result. You can call the cropper method `getResult` to get current stencil coordinates and canvas with cropped image.

Click at the button **Crop Image** below to see this method in action
<getting-result-second-example/>

```js
import { Cropper } from 'vue-advanced-cropper';

export default {
	components: {
		Cropper,
	},
	data() {
		return {
			coordinates: {
				width: 0,
				height: 0,
				left: 0,
				top: 0,
			},
			image: null,
		};
	},
	methods: {
		crop() {
			const { coordinates, canvas, } = this.$refs.cropper.getResult();
			this.coordinates = coordinates;
			// You able to do different manipulations at a canvas
			// but there we just get a cropped image, that can be used 
			// as src for <img/> to preview result
			this.image = canvas.toDataURL();
		},
	},
};
```

```html
<div id="app">
	<cropper
		src="https://images.pexels.com/photos/580012/pexels-photo-580012.jpeg"
		ref="cropper"
	/>
	<button @click="crop">
		Crop
	</button>
</div>
```

## Load image from a disc

The image loading doesn't depend at this library and can be completed by a numerous ways. There will be considered only one of them.

That's what you will get:
<upload-example></upload-example>

```js
import { Cropper } from 'vue-advanced-cropper'

export default {
	components: {
		Cropper,
	},
	data() {
		return {
			image: null,
		};
	},
	methods: {
		reset() {
			this.image = null;
		},
		loadImage(event) {
			// Reference to the DOM input element
			var input = event.target;
			// Ensure that you have a file before attempting to read it
			if (input.files && input.files[0]) {
				// create a new FileReader to read this image and convert to base64 format
				var reader = new FileReader();
				// Define a callback function to run, when FileReader finishes its job
				reader.onload = (e) => {
					// Note: arrow function used here, so that "this.imageData" refers to the imageData of Vue component
					// Read image as base64 and set to imageData
					this.image = e.target.result;
				};
				// Start the reader job - read file as a data url (base64 format)
				reader.readAsDataURL(input.files[0]);
			}
		},
	},
};
```

```html
<div id="app">
	<div class="upload-example">
		<cropper
			class="upload-example-cropper"
			:src="image"
		/>
		<div class="button-wrapper">
			<span class="button" @click="$refs.file.click()">
				<input type="file" ref="file" @change="loadImage($event)" accept="image/*">
				Load image
			</span>
		</div>
	</div>
</div>
```

```css
.upload-example-cropper {
	border: solid 1px #EEE;
	height: 300px;
	width: 100%;
}

.button-wrapper {
	display: flex;
	justify-content: center;
	margin-top: 17px;
}

.button {
	color: white;
	font-size: 16px;
	padding: 10px 20px;
	background: #3fb37f;
	cursor: pointer;
	transition: background 0.5s;
}

.button:hover {
	background: #38d890;
}

.button input {
	display: none;
}
```


## Upload image to a server

The preferred method to upload image to the server is using [a blob](https://developer.mozilla.org/ru/docs/Web/API/HTMLCanvasElement/toBlob) (for IE-11 you should use polyfill). 
The detailed explanation why you shouldn't use the data-url you can read [in the great answer](https://stackoverflow.com/questions/59020799/which-function-should-i-use-todataurl-or-toblob/59025746#59025746) on Stackoverflow.

There are different approaches to implement image uploading because it depends on your backend. The one of them is presented below.

<upload-to-server-example></upload-to-server-example>

*Look the network section in your developer tools to examine the sent request.*

```js
import { Cropper } from 'vue-advanced-cropper';

export default {
	components: {
		Cropper,
	},
	data() {
		return {
			image: 'https://images.unsplash.com/photo-1591273531346-ba9262aa2da6?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=751&q=80',
		};
	},
	methods: {
		reset() {
			this.image = null;
		},
		uploadImage() {
			const { canvas } = this.$refs.cropper.getResult();
			if (canvas) {
				const form = new FormData();
				canvas.toBlob(blob => {
					form.append('file', blob);
					// You can use axios, superagent and other libraries instead here
					fetch('http://example.com/upload/', {
						method: 'POST',
						body: form,
					});
					// Perhaps you should add the setting appropriate file format here
				}, 'image/jpeg');
			}
		},
	},
};
```
