<script>
import { DraggableElement, DraggableArea, PreviewResult, ResizeEvent } from 'vue-advanced-cropper';

export default {
	name: 'CircleStencil',
	components: {
		PreviewResult,
		DraggableArea,
		DraggableElement,
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
	},
	computed: {
		style() {
			const { height, width, left, top } = this.stencilCoordinates;
			return {
				width: `${width}px`,
				height: `${height}px`,
				left: `0px`,
				top: `0px`,
				transform: `translate(${left}px, ${top}px)`,
			};
		},
	},
	methods: {
		onMove(moveEvent) {
			this.$emit('move', moveEvent);
		},
		onHandlerMove(dragEvent) {
			const shift = dragEvent.shift();
			const widthResize = shift.left / 2;
			const heightResize = -shift.top / 2;
			this.$emit(
				'resize',
				new ResizeEvent(
					{
						left: widthResize,
						right: widthResize,
						top: heightResize,
						bottom: heightResize,
					},
					{
						compensate: true,
					},
				),
			);
		},
		aspectRatios() {
			return {
				minimum: 1,
				maximum: 1,
			};
		},
	},
};
</script>

<template>
	<div class="circle-stencil" :style="style">
		<draggable-element class="circle-stencil__handler" @drag="onHandlerMove">
			<img :src="require('./assets/handler.svg')" class="circle-stencil__icon" alt="" />
		</draggable-element>
		<draggable-area @move="onMove">
			<preview-result class="circle-stencil__preview" :image="image" :stencil-coordinates="stencilCoordinates" />
		</draggable-area>
	</div>
</template>

<style lang="scss">
.circle-stencil {
	border-radius: 50%;
	cursor: move;
	position: absolute;
	border: dashed 2px white;
	box-sizing: border-box;
	&__icon {
		user-select: none;
		pointer-events: none;
	}
	&__handler {
		position: absolute;
		right: 15%;
		top: 14%;
		z-index: 1;
		cursor: ne-resize;
		width: 30px;
		height: 30px;
		display: flex;
		align-items: center;
		justify-content: center;
		transform: translate(50%, -50%);
	}
	&__preview {
		border-radius: 50%;
		overflow: hidden;
	}
}
</style>
