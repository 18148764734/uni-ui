<template>
	<view class="uni-numbox">
		<view @click="_calcValue('minus')" class="uni-numbox__minus uni-numbox-btns" :style="{background}">
			<!-- #ifndef H5-->
			<text class="numicon uni-numbox--text" :class="{ 'uni-numbox--disabled': inputValue <= min || disabled }">{{'\ue999'}}</text>
			<!-- #endif -->
			<!-- #ifdef H5 -->
			<uni-icons class="numicon uni-numbox--text" :class="{ 'uni-numbox--disabled': inputValue <= min || disabled }"
				:style="{inputValue <= min?:color}" custom-prefix="numbox" font-family="numbox" type="numMinus"></uni-icons>
			<!-- #endif -->
		</view>
		<input :disabled="disabled" @focus="_onFocus" @blur="_onBlur" class="uni-numbox__value"
			:type="step<1?'digit':'number'" v-model="inputValue" :style="{background, color, width:widthWithPx}" />
		<view @click="_calcValue('plus')" class="uni-numbox__plus uni-numbox-btns" :style="{background}">
			<!-- #ifndef H5-->
			<text class="numicon uni-numbox--text" :class="{ 'uni-numbox--disabled': inputValue >= max || disabled }"
				:style="{color}">{{'\ue888'}}</text>
			<!-- #endif -->
			<!-- #ifdef H5 -->
			<uni-icons class="numicon uni-numbox--text" :class="{ 'uni-numbox--disabled': inputValue >= max || disabled }"
				:style="{color}" custom-prefix="numbox" font-family="numbox" type="numPlus"></uni-icons>
			<!-- #endif -->
		</view>
	</view>
</template>
<script>
	/**
	 * NumberBox 数字输入框
	 * @description 带加减按钮的数字输入框
	 * @tutorial https://ext.dcloud.net.cn/plugin?id=31
	 * @property {Number} value 输入框当前值
	 * @property {Number} min 最小值
	 * @property {Number} max 最大值
	 * @property {Number} step 每次点击改变的间隔大小
	 * @property {String} background 背景色
	 * @property {String} color 字体颜色（前景色）
	 * @property {Number} width 输入框宽度(单位:px)
	 * @property {String} size 大小 = ["mini"|"large"|"default"]
	 * @property {Boolean} disabled = [true|false] 是否为禁用状态
	 * @event {Function} change 输入框值改变时触发的事件，参数为输入框当前的 value
	 * @event {Function} focus 输入框聚焦时触发的事件，参数为 event 对象
	 * @event {Function} blur 输入框失焦时触发的事件，参数为 event 对象
	 */

	export default {
		name: "UniNumberBox",
		emits: ['change', 'input', 'update:modelValue', 'blur', 'focus'],
		props: {
			value: {
				type: [Number, String],
				default: 1
			},
			modelValue: {
				type: [Number, String],
				default: 1
			},
			min: {
				type: Number,
				default: 0
			},
			size: {
				type: String,
				default: 'default'
			},
			max: {
				type: Number,
				default: 100
			},
			step: {
				type: Number,
				default: 1
			},
			background: {
				type: String,
				default: '#f5f5f5'
			},
			color: {
				type: String,
				default: '#333'
			},
			disabled: {
				type: Boolean,
				default: false
			},
			width: {
				type: Number,
				default: 40,
			}
		},
		data() {
			return {
				inputValue: 0
			};
		},
		watch: {
			value(val) {
				this.inputValue = +val;
			},
			modelValue(val) {
				this.inputValue = +val;
			}
		},
		computed: {
			widthWithPx() {
				return this.width + 'px';
			}
		},
		created() {
			if (this.value === 1) {
				this.inputValue = +this.modelValue;
			}
			if (this.modelValue === 1) {
				this.inputValue = +this.value;
			}
			// #ifdef APP-NVUE
			var domModule = weex.requireModule("dom");
			domModule.addRule('fontFace', {
				'fontFamily': 'numbox',
				'src': "url('data:font/truetype;charset=utf-8;base64,AAEAAAALAIAAAwAwR1NVQiCLJXoAAAE4AAAAVE9TLzI+nUxpAAABjAAAAGBjbWFwd77V3QAAAfgAAAGGZ2x5ZrQXKckAAAOIAAAAnGhlYWQnhSUuAAAA4AAAADZoaGVhB94DhAAAALwAAAAkaG10eAwAAAAAAAHsAAAADGxvY2EAGABOAAADgAAAAAhtYXhwAQ4AMAAAARgAAAAgbmFtZRCjPLAAAAQkAAACZ3Bvc3Tf+VfmAAAGjAAAADYAAQAAA4D/gABcBAAAAAAABAAAAQAAAAAAAAAAAAAAAAAAAAMAAQAAAAEAAO37zwZfDzz1AAsEAAAAAADiMvCYAAAAAOIy8JgAAP/ABAADQQAAAAgAAgAAAAAAAAABAAAAAwAkAAEAAAAAAAIAAAAKAAoAAAD/AAAAAAAAAAEAAAAKADAAPgACREZMVAAObGF0bgAaAAQAAAAAAAAAAQAAAAQAAAAAAAAAAQAAAAFsaWdhAAgAAAABAAAAAQAEAAQAAAABAAgAAQAGAAAAAQAAAAQEAAGQAAUAAAKJAswAAACPAokCzAAAAesAMgEIAAACAAUDAAAAAAAAAAAAAAAAAAAAAAAAAAAAAFBmRWQAwOiI6ZkDgP+AAAAD3ACAAAAAAQAAAAAAAAAAAAAAAAACBAAAAAQAAAAEAAAAAAAABQAAAAMAAAAsAAAABAAAAV4AAQAAAAAAWAADAAEAAAAsAAMACgAAAV4ABAAsAAAABgAEAAEAAuiI6Zn//wAA6Ijpmf//AAAAAAABAAYABgAAAAIAAQAAAQYAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADAAAAAAAKAAAAAAAAAACAADoiAAA6IgAAAACAADpmQAA6ZkAAAABAAAAAAAAABgATgABAAAAAANxAbEACwAAASEiBhQWMyEyNjQmA0D9gBQcHBQCgBQcHAGwHCgcHCgcAAABAAD/wAPBA0EAIwAAARUUBiMhERQGKwEiJjURISImPQE0NjMhETQ2OwEyFhURITIWA8AcFP6lHBQJFB3+pRQcHBQBWx0UCRQcAVsTHQGECRQc/qUUHBwUAVscFAkUHQFbFBwdE/6lHQAAAAAAABIA3gABAAAAAAAAABMAAAABAAAAAAABAAgAEwABAAAAAAACAAcAGwABAAAAAAADAAgAIgABAAAAAAAEAAgAKgABAAAAAAAFAAsAMgABAAAAAAAGAAgAPQABAAAAAAAKACsARQABAAAAAAALABMAcAADAAEECQAAACYAgwADAAEECQABABAAqQADAAEECQACAA4AuQADAAEECQADABAAxwADAAEECQAEABAA1wADAAEECQAFABYA5wADAAEECQAGABAA/QADAAEECQAKAFYBDQADAAEECQALACYBY0NyZWF0ZWQgYnkgaWNvbmZvbnRpY29uZm9udFJlZ3VsYXJpY29uZm9udGljb25mb250VmVyc2lvbiAxLjBpY29uZm9udEdlbmVyYXRlZCBieSBzdmcydHRmIGZyb20gRm9udGVsbG8gcHJvamVjdC5odHRwOi8vZm9udGVsbG8uY29tAEMAcgBlAGEAdABlAGQAIABiAHkAIABpAGMAbwBuAGYAbwBuAHQAaQBjAG8AbgBmAG8AbgB0AFIAZQBnAHUAbABhAHIAaQBjAG8AbgBmAG8AbgB0AGkAYwBvAG4AZgBvAG4AdABWAGUAcgBzAGkAbwBuACAAMQAuADAAaQBjAG8AbgBmAG8AbgB0AEcAZQBuAGUAcgBhAHQAZQBkACAAYgB5ACAAcwB2AGcAMgB0AHQAZgAgAGYAcgBvAG0AIABGAG8AbgB0AGUAbABsAG8AIABwAHIAbwBqAGUAYwB0AC4AaAB0AHQAcAA6AC8ALwBmAG8AbgB0AGUAbABsAG8ALgBjAG8AbQAAAgAAAAAAAAAKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADAQIBAwEEAAVtaW51cwRwbHVzAAAAAA==')"
			})
			// #endif
		},
		methods: {
			_calcValue(type) {
				if (this.disabled) {
					return;
				}
				const scale = this._getDecimalScale();
				let value = this.inputValue * scale;
				let step = this.step * scale;
				if (type === "minus") {
					value -= step;
					if (value < (this.min * scale)) {
						return;
					}
					if (value > (this.max * scale)) {
						value = this.max * scale
					}
				}

				if (type === "plus") {
					value += step;
					if (value > (this.max * scale)) {
						return;
					}
					if (value < (this.min * scale)) {
						value = this.min * scale
					}
				}

				this.inputValue = (value / scale).toFixed(String(scale).length - 1);
				// TODO vue2 兼容
				this.$emit("input", +this.inputValue);
				// TODO vue3 兼容
				this.$emit("update:modelValue", +this.inputValue);
				this.$emit("change", +this.inputValue);
			},
			_getDecimalScale() {

				let scale = 1;
				// 浮点型
				if (~~this.step !== this.step) {
					scale = Math.pow(10, String(this.step).split(".")[1].length);
				}
				return scale;
			},
			_onBlur(event) {
				this.$emit('blur', event)
				let value = event.detail.value;
				if (isNaN(value)) {
					this.inputValue = this.min;
					return;
				}
				value = +value;
				if (value > this.max) {
					value = this.max;
				} else if (value < this.min) {
					value = this.min;
				}
				const scale = this._getDecimalScale();
				this.inputValue = value.toFixed(String(scale).length - 1);
				this.$emit("input", +this.inputValue);
				this.$emit("update:modelValue", +this.inputValue);
				this.$emit("change", +this.inputValue);
			},
			_onFocus(event) {
				this.$emit('focus', event)
			}
		}
	};
</script>
<style lang="scss">
	$box-height: 26px;
	$bg: #f5f5f5;
	$num-bg: #9e9e9e;
	$br: 5px;
	$color: #333;
	$border-color: #a6a7ab;

	/* #ifndef APP-NVUE */
	@font-face {
		font-family: numbox;
		font-size: 16px;
		font-style: normal;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		src: url('data:font/truetype;charset=utf-8;base64,AAEAAAALAIAAAwAwR1NVQiCLJXoAAAE4AAAAVE9TLzI+nUxpAAABjAAAAGBjbWFwd77V3QAAAfgAAAGGZ2x5ZrQXKckAAAOIAAAAnGhlYWQnhSUuAAAA4AAAADZoaGVhB94DhAAAALwAAAAkaG10eAwAAAAAAAHsAAAADGxvY2EAGABOAAADgAAAAAhtYXhwAQ4AMAAAARgAAAAgbmFtZRCjPLAAAAQkAAACZ3Bvc3Tf+VfmAAAGjAAAADYAAQAAA4D/gABcBAAAAAAABAAAAQAAAAAAAAAAAAAAAAAAAAMAAQAAAAEAAO37zwZfDzz1AAsEAAAAAADiMvCYAAAAAOIy8JgAAP/ABAADQQAAAAgAAgAAAAAAAAABAAAAAwAkAAEAAAAAAAIAAAAKAAoAAAD/AAAAAAAAAAEAAAAKADAAPgACREZMVAAObGF0bgAaAAQAAAAAAAAAAQAAAAQAAAAAAAAAAQAAAAFsaWdhAAgAAAABAAAAAQAEAAQAAAABAAgAAQAGAAAAAQAAAAQEAAGQAAUAAAKJAswAAACPAokCzAAAAesAMgEIAAACAAUDAAAAAAAAAAAAAAAAAAAAAAAAAAAAAFBmRWQAwOiI6ZkDgP+AAAAD3ACAAAAAAQAAAAAAAAAAAAAAAAACBAAAAAQAAAAEAAAAAAAABQAAAAMAAAAsAAAABAAAAV4AAQAAAAAAWAADAAEAAAAsAAMACgAAAV4ABAAsAAAABgAEAAEAAuiI6Zn//wAA6Ijpmf//AAAAAAABAAYABgAAAAIAAQAAAQYAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADAAAAAAAKAAAAAAAAAACAADoiAAA6IgAAAACAADpmQAA6ZkAAAABAAAAAAAAABgATgABAAAAAANxAbEACwAAASEiBhQWMyEyNjQmA0D9gBQcHBQCgBQcHAGwHCgcHCgcAAABAAD/wAPBA0EAIwAAARUUBiMhERQGKwEiJjURISImPQE0NjMhETQ2OwEyFhURITIWA8AcFP6lHBQJFB3+pRQcHBQBWx0UCRQcAVsTHQGECRQc/qUUHBwUAVscFAkUHQFbFBwdE/6lHQAAAAAAABIA3gABAAAAAAAAABMAAAABAAAAAAABAAgAEwABAAAAAAACAAcAGwABAAAAAAADAAgAIgABAAAAAAAEAAgAKgABAAAAAAAFAAsAMgABAAAAAAAGAAgAPQABAAAAAAAKACsARQABAAAAAAALABMAcAADAAEECQAAACYAgwADAAEECQABABAAqQADAAEECQACAA4AuQADAAEECQADABAAxwADAAEECQAEABAA1wADAAEECQAFABYA5wADAAEECQAGABAA/QADAAEECQAKAFYBDQADAAEECQALACYBY0NyZWF0ZWQgYnkgaWNvbmZvbnRpY29uZm9udFJlZ3VsYXJpY29uZm9udGljb25mb250VmVyc2lvbiAxLjBpY29uZm9udEdlbmVyYXRlZCBieSBzdmcydHRmIGZyb20gRm9udGVsbG8gcHJvamVjdC5odHRwOi8vZm9udGVsbG8uY29tAEMAcgBlAGEAdABlAGQAIABiAHkAIABpAGMAbwBuAGYAbwBuAHQAaQBjAG8AbgBmAG8AbgB0AFIAZQBnAHUAbABhAHIAaQBjAG8AbgBmAG8AbgB0AGkAYwBvAG4AZgBvAG4AdABWAGUAcgBzAGkAbwBuACAAMQAuADAAaQBjAG8AbgBmAG8AbgB0AEcAZQBuAGUAcgBhAHQAZQBkACAAYgB5ACAAcwB2AGcAMgB0AHQAZgAgAGYAcgBvAG0AIABGAG8AbgB0AGUAbABsAG8AIABwAHIAbwBqAGUAYwB0AC4AaAB0AHQAcAA6AC8ALwBmAG8AbgB0AGUAbABsAG8ALgBjAG8AbQAAAgAAAAAAAAAKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAADAQIBAwEEAAVtaW51cwRwbHVzAAAAAA==') format('truetype');
	}

	.numPlus:before {
		content: "\e888";
	}

	.numMinus:before {
		content: "\e999";
	}

	/* #endif */


	.numicon {
		font-family: numbox;
		font-size: 16px;
		color: #ffc649;
	}

	.uni-numbox {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
	}

	.uni-numbox-btns {
		/* #ifndef APP-NVUE */
		display: flex;
		/* #endif */
		flex-direction: row;
		align-items: center;
		justify-content: center;
		padding: 0 8px;
		background-color: $bg;
		border: $border-color 0.5px solid;
		/* #ifdef H5 */
		cursor: pointer;
		/* #endif */
	}

	.uni-numbox__value {
		background-color: $num-bg;
		width: 40px;
		height: $box-height;
		text-align: center;
		font-size: 14px;
		border: $border-color 0.5px solid;
		border-left-width: 0;
		border-right-width: 0;
		color: $color;
	}

	.uni-numbox__minus {
		border-top-left-radius: $br;
		border-bottom-left-radius: $br;
	}

	.uni-numbox__plus {
		border-top-right-radius: $br;
		border-bottom-right-radius: $br;
	}

	.uni-numbox--text {
		// fix nvue
		line-height: 20px;
		font-size: 16px;
		font-weight: 300;
		display: flex;
		color: $color;
	}

	.uni-numbox .uni-numbox--disabled {
		color: #efffb9;
		/* #ifdef H5 */
		cursor: not-allowed;
		/* #endif */
	}
</style>
