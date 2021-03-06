<script>
import classnames from 'classnames';
import bem from 'easy-bem';
import { PreviewResult, BoundingBox, DraggableArea } from '../service';
import { SimpleHandler } from '../handlers';
import { SimpleLine } from '../lines';

const cn = bem('vue-rectangle-stencil');

export default {
	name: 'RectangleStencil',
	components: {
		PreviewResult,
		BoundingBox,
		DraggableArea,
	},
	props: {
		image: {
			type: Object,
		},
		resultCoordinates: {
			type: Object,
		},
		stencilCoordinates: {
			type: Object,
		},
		handlers: {
			type: Object,
		},
		handlersComponent: {
			type: [Object, String],
			default() {
				return SimpleHandler;
			},
		},
		lines: {
			type: Object,
		},
		linesComponent: {
			type: [Object, String],
			default() {
				return SimpleLine;
			},
		},
		aspectRatio: {
			type: [Number, String],
		},
		minAspectRatio: {
			type: [Number, String],
		},
		maxAspectRatio: {
			type: [Number, String],
		},
		movable: {
			type: Boolean,
			default: true,
		},
		resizable: {
			type: Boolean,
			default: true,
		},
		transitions: {
			type: Object,
		},
		movingClass: {
			type: String,
		},
		resizingClass: {
			type: String,
		},
		previewClass: {
			type: String,
		},
		boundingBoxClass: {
			type: String,
		},
		linesClasses: {
			type: Object,
			default() {
				return {};
			},
		},
		linesWrappersClasses: {
			type: Object,
			default() {
				return {};
			},
		},
		handlersClasses: {
			type: Object,
			default() {
				return {};
			},
		},
		handlersWrappersClasses: {
			type: Object,
			default() {
				return {};
			},
		},
	},
	data() {
		return {
			moving: false,
			resizing: false,
		};
	},
	computed: {
		classes() {
			return {
				stencil: classnames(
					cn({ movable: this.movable, moving: this.moving, resizing: this.resizing }),
					this.moving && this.movingClass,
					this.resizing && this.resizingClass,
				),
				preview: classnames(cn('preview'), this.previewClass),
				boundingBox: classnames(cn('bounding-box'), this.boundingBoxClass),
			};
		},
		style() {
			const { height, width, left, top } = this.stencilCoordinates;

			const style = {
				width: `${width}px`,
				height: `${height}px`,
				left: `${0}px`,
				top: `${0}px`,
				transform: `translate(${left}px, ${top}px)`,
			};

			if (this.transitions && this.transitions.enabled) {
				style.transition = `${this.transitions.time}ms ${this.transitions.timingFunction}`;
			}
			return style;
		},
	},
	methods: {
		onMove(moveEvent) {
			this.$emit('move', moveEvent);
			this.moving = true;
		},
		onMoveEnd() {
			this.$emit('move-end');
			this.moving = false;
		},
		onResize(resizeEvent) {
			this.$emit('resize', resizeEvent);
			this.resizing = true;
		},
		onResizeEnd() {
			this.$emit('resize-end');
			this.resizing = false;
		},
		aspectRatios() {
			return {
				minimum: this.aspectRatio || this.minAspectRatio,
				maximum: this.aspectRatio || this.maxAspectRatio,
			};
		},
	},
};
</script>

<template>
	<div :class="classes.stencil" :style="style">
		<BoundingBox
			:class="classes.boundingBox"
			:handlers="handlers"
			:handlers-component="handlersComponent"
			:handlers-classes="handlersClasses"
			:handlers-wrappers-classes="handlersWrappersClasses"
			:lines="lines"
			:lines-component="linesComponent"
			:lines-classes="linesClasses"
			:lines-wrappers-classes="linesWrappersClasses"
			:resizable="resizable"
			@resize="onResize"
			@resize-end="onResizeEnd"
		>
			<DraggableArea :movable="movable" @move="onMove" @move-end="onMoveEnd">
				<PreviewResult
					:image="image"
					:class="classes.preview"
					:transitions="transitions"
					:stencil-coordinates="stencilCoordinates"
				/>
			</DraggableArea>
		</BoundingBox>
	</div>
</template>

<style lang="scss">
.vue-rectangle-stencil {
	position: absolute;
	height: 100%;
	width: 100%;
	box-sizing: border-box;
	&--movable {
		cursor: move;
	}
}
</style>
