<style>
	.v-select {
		/*position: relative;*/
	}
	.v-select-wrap{
		position:relative;
	}
	.v-select-wrap .open-indicator {
		position: absolute;
		top: 50%;
		right: .875rem;
		margin-top:-.5rem;
		display: inline-block;
		cursor: pointer;
		pointer-events: all;
		transition: all 150ms cubic-bezier(1.000, -0.115, 0.975, 0.855);
		transition-timing-function: cubic-bezier(1.000, -0.115, 0.975, 0.855);
		opacity: 1;
		transition: opacity .1s;
	}

	.v-select-wrap.loading .open-indicator {
		opacity: 0;
	}

	.v-select-wrap .open-indicator:before {
		content: "\f0d8";
		vertical-align: top;
		display: inline-block;
		font: normal normal normal 14px/1 FontAwesome;
		font-size: inherit;
		text-rendering: auto;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
		transform: rotate(180deg);
		transition: all 150ms cubic-bezier(1.000, -0.115, 0.975, 0.855);
		transition-timing-function: cubic-bezier(1.000, -0.115, 0.975, 0.855);
	}

	.v-select-wrap.open .open-indicator {
		bottom: 1px;
	}

	.v-select-wrap.open .open-indicator:before {
		transform: rotate(360deg);
	}

	.v-select-wrap .dropdown-toggle {
		display: block;
		width: 100%;
		padding: .5rem .75rem;
		font-size: 1rem;
		line-height: 1.25;
		min-height:2.5rem;
		color: #55595c;
		background-color: #fff;
		background-image: none;
		background-clip: padding-box;
		border: 1px solid rgba(0, 0, 0, 0.15);
		border-radius: 0;
		-webkit-appearance: none;
	}

	.v-select-wrap .dropdown-toggle:after{
		display:unset;
		margin-left:0;
		content:"";
		border-top:0;
		border-left:0;
		border-right:0;
	}

	.v-select-wrap.searchable .dropdown-toggle {
		cursor: text;
	}

	.v-select-wrap.open .dropdown-toggle {
		border-bottom: none;
		border-bottom-left-radius: 0;
		border-bottom-right-radius: 0;
	}

	.v-select-wrap > .dropdown-menu {
		margin: 0;
		width: 100%;
		overflow-y: scroll;
		border-top: none;
		border-top-left-radius: 0;
		border-top-right-radius: 0;
	}

	.v-select-wrap > .dropdown-menu li.active{
		background: #1a1a1a;
		color:#fff;
	}
	.v-select-wrap > .dropdown-menu li a{
		display:block;
		padding:.25rem .5rem;
	}

	.v-select .selected-tag {
		color: #000;
		float:left;
		font-size:16px;
	}

	.v-select .multiple-selected-lbl-wrap:before{
		content: "Vendors Added";
		display: inline-block;
		margin:1rem 0 .5rem;
		font-weight:bold;
	}
	.v-select .multiple-selected-tag-wrap{
		padding:1rem 0;
	}

	.v-select .multiple-selected-tag{
		display:block;
		padding: .5rem .75rem;
		margin-bottom: 1rem;
		border: 1px solid #d0d0d0;
		background: #e1e1e1;
		color:#505050;
	}
	.v-select .multiple-selected-tag .close {
		opacity:1;
		color: #b40000;
		line-height:.75;
	}

	.v-select input[type=search],
	.v-select input[type=search]:focus {
		display: inline-block;
		border: none;
		outline: none;
		margin: 0;
		padding: 0 .5em;
		width: 10em;
		max-width: 100%;
		background: none;
		position: relative;
		box-shadow: none;
		float: left;
		clear: none;
	}

	.v-select input[type=search]:disabled {
		cursor: pointer;
	}

	.v-select li a {
		cursor: pointer;
	}

	.v-select .active a {
		background: #005a2c;
		color: #fff;
	}

	.v-select .highlight a,
	.v-select li:hover > a:hover {
		background: #e1e1e1;
		color: #000;
	}

	.v-select .spinner {
		opacity: 0;
		position: absolute;
		top: 5px;
		right: 10px;
		font-size: 5px;
		text-indent: -9999em;
		overflow: hidden;
		border-top: .9em solid rgba(100, 100, 100, .1);
		border-right: .9em solid rgba(100, 100, 100, .1);
		border-bottom: .9em solid rgba(100, 100, 100, .1);
		border-left: .9em solid rgba(60, 60, 60, .45);
		transform: translateZ(0);
		animation: vSelectSpinner 1.1s infinite linear;
		transition: opacity .1s;
	}

	.v-select.loading .spinner {
		opacity: 1;
	}

	.v-select .spinner,
	.v-select .spinner:after {
		border-radius: 50%;
		width: 5em;
		height: 5em;
	}

	@-webkit-keyframes vSelectSpinner {
		0% {
			transform: rotate(0deg);
		}
		100% {
			transform: rotate(360deg);
		}
	}

	@keyframes vSelectSpinner {
		0% {
			transform: rotate(0deg);
		}
		100% {
			transform: rotate(360deg);
		}
	}
</style>
<template>
	<div class="dropdown v-select" >
		<div class="v-select-wrap" :class="dropdownClasses">
			<div v-el:toggle @mousedown.prevent="toggleDropdown" class="dropdown-toggle clearfix" type="button">
				<span class="form-control" v-if="!searchable && isValueEmpty">{{ placeholder }}</span>
				<span v-if="!multiple">
					<span class="selected-tag" v-for="option in valueAsArray" track-by="$index">{{ getOptionLabel(option) }}</span>
				</span>	
				<input v-el:search :debounce="debounce" v-model="search" v-show="searchable" @keydown.delete="maybeDeleteValue" @keyup.esc="onEscape"
					@keydown.up.prevent="typeAheadUp" @keydown.down.prevent="typeAheadDown" @keyup.enter.prevent="typeAheadSelect" @blur="open = false"
					@focus="open = true" type="search" class="form-control" :placeholder="searchPlaceholder" :style="{ width: isValueEmpty ? '100%' : 'auto' }"
					>

				<i v-el:open-indicator role="presentation" class="open-indicator"></i>

				<slot name="spinner">
					<div class="spinner" v-show="loading">Loading...</div>
				</slot>
			</div>

			<ul v-el:dropdown-menu v-show="open" :transition="transition" class="dropdown-menu" :style="{ 'max-height': maxHeight }">
				<li v-for="option in filteredOptions" track-by="$index" :class="{ active: isOptionSelected(option), highlight: $index === typeAheadPointer }"
					@mouseover="typeAheadPointer = $index">
					<a @mousedown.prevent="select(option)">
						{{ getOptionLabel(option) }}
					</a>
				</li>
				<li transition="fade" v-if="!filteredOptions.length" class="divider"></li>
				<li transition="fade" v-if="!filteredOptions.length" class="text-center">
					<slot name="no-options">Sorry, no matching options.</slot>
				</li>
			</ul>
		</div>
		<div class="multiple-selected-tag-wrap" v-if="multiple" v-bind:class="{'multiple-selected-lbl-wrap' : valueAsArray != null}" track-by="$index">
			<span class="multiple-selected-tag clearfix" v-for="option in valueAsArray" track-by="$index">
				{{ getOptionLabel(option) }}
				<button  @click="select(option)" type="button" class="close"><span aria-hidden="true">&times;</span></button>
			</span>
		</div>
	</div>
	
</template>


<script type="text/babel">
	import pointerScroll from '../mixins/pointerScroll'
	import typeAheadPointer from '../mixins/typeAheadPointer'
	import ajax from '../mixins/ajax'

	export default {
		mixins: [pointerScroll, typeAheadPointer, ajax],

		props: {
			/**
			 * Contains the currently selected value. Very similar to a
			 * `value` attribute on an <input>. In most cases, you'll want
			 * to set this as a two-way binding, using :value.sync. However,
			 * this will not work with Vuex, in which case you'll need to use
			 * the onChange callback property.
			 * @type {Object||String||null}
			 */
			value: {
				default: null
			},

			/**
			 * An array of strings or objects to be used as dropdown choices.
			 * If you are using an array of objects, vue-select will look for
			 * a `label` key (ex. [{label: 'This is Foo', value: 'foo'}]). A
			 * custom label key can be set with the `label` prop.
			 * @type {Object}
			 */
			options: {
				type: Array,
				default() {
					return []
				},
			},

			/**
			 * Sets the max-height property on the dropdown list.
			 * @deprecated
			 * @type {String}
			 */
			maxHeight: {
				type: String,
				default: '400px'
			},

			/**
			 * Enable/disable filtering the options.
			 * @type {Boolean}
			 */
			searchable: {
				type: Boolean,
				default: true
			},

			/**
			 * Equivalent to the `multiple` attribute on a `<select>` input.
			 * @type {Object}
			 */
			multiple: {
				type: Boolean,
				default: false
			},

			/**
			 * Equivalent to the `placeholder` attribute on an `<input>`.
			 * @type {Object}
			 */
			placeholder: {
				type: String,
				default: ''
			},

			/**
			 * Sets a Vue transition property on the `.dropdown-menu`. vue-select
			 * does not include CSS for transitions, you'll need to add them yourself.
			 * @type {String}
			 */
			transition: {
				type: String,
				default: 'expand'
			},

			/**
			 * Enables/disables clearing the search text when an option is selected.
			 * @type {Boolean}
			 */
			clearSearchOnSelect: {
				type: Boolean,
				default: true
			},

			/**
			 * Tells vue-select what key to use when generating option
			 * labels when each `option` is an object.
			 * @type {String}
			 */
			label: {
				type: String,
				default: 'label'
			},

			/**
			 * Callback to generate the label text. If {option}
			 * is an object, returns option[this.label] by default.
			 * @param  {Object || String} option
			 * @return {String}
			 */
			getOptionLabel: {
				type: Function,
				default(option) {
					if (typeof option === 'object') {
						if (this.label && option[this.label]) {
							return option[this.label]
						}
					}
					return option;
				}
			},

			/**
			 * An optional callback function that is called each time the selected
			 * value(s) change. When integrating with Vuex, use this callback to trigger
			 * an action, rather than using :value.sync to retreive the selected value.
			 * @type {Function}
			 * @default {null}
			 */
			onChange: Function,

			/**
			 * Enable/disable creating options from searchInput.
			 * @type {Boolean}
			 */
			taggable: {
				type: Boolean,
				default: false
			},

			/**
			 * When true, newly created tags will be added to
			 * the options list.
			 * @type {Boolean}
			 */
			pushTags: {
				type: Boolean,
				default: false
			},

			/**
			 * User defined function for adding Options
			 * @type {Function}
			 */
			createOption: {
				type: Function,
				default: function (newOption) {
					if (typeof this.options[0] === 'object') {
						return {[this.label]: newOption}
					}
					return newOption
				}
			},

			/**
			 * When false, updating the options will not reset the select value
			 * @type {Boolean}
			 */
			resetOnOptionsChange: {
				type: Boolean,
				default: false
			},
		},

		data() {
			return {
				search: '',
				open: false
			}
		},

		watch: {
			value(val, old) {
				if (this.multiple) {
					this.onChange ? this.onChange(val) : null
				} else {
					this.onChange && val !== old ? this.onChange(val) : null
				}
			},
			options() {
				if (!this.taggable && this.resetOnOptionsChange) {
					this.$set('value', this.multiple ? [] : null)
				}
			},
			multiple(val) {
				this.$set('value', val ? [] : null)
			}
		},

		methods: {

			/**
			 * Select a given option.
			 * @param  {Object||String} option
			 * @return {void}
			 */
			select(option) {
				if (this.isOptionSelected(option)) {
					this.deselect(option)
				} else {
					if (this.taggable && !this.optionExists(option)) {
						option = this.createOption(option)

						if (this.pushTags) {
							this.options.push(option)
						}
					}

					if (this.multiple) {
						if (!this.value) {
							this.$set('value', [option])
						} else {
							this.value.push(option)
						}
					} else {
						this.value = option
					}
				}

				this.onAfterSelect(option)
			},

			/**
			 * De-select a given option.
			 * @param  {Object||String} option
			 * @return {void}
			 */
			deselect(option) {
				if (this.multiple) {
					let ref = -1
					this.value.forEach((val) => {
						if (val === option || typeof val === 'object' && val[this.label] === option[this.label]) {
							ref = val
						}
					})
					this.value.$remove(ref)
				} else {
					this.value = null
				}
			},

			/**
			 * Called from this.select after each selection.
			 * @param  {Object||String} option
			 * @return {void}
			 */
			onAfterSelect(option) {
				if (!this.multiple) {
					this.open = !this.open
					this.$els.search.blur()
				}

				if (this.clearSearchOnSelect) {
					this.search = ''
				}
			},

			/**
			 * Toggle the visibility of the dropdown menu.
			 * @param  {Event} e
			 * @return {void}
			 */
			toggleDropdown(e) {
				if (e.target === this.$els.openIndicator || e.target === this.$els.search || e.target === this.$els.toggle || e.target === this.$el) {
					if (this.open) {
						this.$els.search.blur() // dropdown will close on blur
					} else {
						this.open = true
						this.$els.search.focus()
					}
				}
			},

			/**
			 * Check if the given option is currently selected.
			 * @param  {Object||String}  option
			 * @return {Boolean}         True when selected || False otherwise
			 */
			isOptionSelected(option) {
				if (this.multiple && this.value) {
					let selected = false
					this.value.forEach(opt => {
						if (typeof opt === 'object' && opt[this.label] === option[this.label]) {
							selected = true
						} else if (opt === option) {
							selected = true
						}
					})
					return selected
				}

				return this.value === option
			},

			/**
			 * If there is any text in the search input, remove it.
			 * Otherwise, blur the search input to close the dropdown.
			 * @return {[type]} [description]
			 */
			onEscape() {
				if (!this.search.length) {
					this.$els.search.blur()
				} else {
					this.search = ''
				}
			},

			/**
			 * Delete the value on Delete keypress when there is no
			 * text in the search input, & there's tags to delete
			 * @return {this.value}
			 */
			maybeDeleteValue() {
				if (!this.$els.search.value.length && this.value) {
					return this.multiple ? this.value.pop() : this.$set('value', null)
				}
			},

			/**
			 * Determine if an option exists
			 * within this.options array.
			 *
			 * @param  {Object || String} option
			 * @return {boolean}
			 */
			optionExists(option) {
				let exists = false

				this.options.forEach(opt => {
					if (typeof opt === 'object' && opt[this.label] === option) {
						exists = true
					} else if (opt === option) {
						exists = true
					}
				})

				return exists
			}
		},

		computed: {

			/**
			 * Classes to be output on .dropdown
			 * @return {Object}
			 */
			dropdownClasses() {
				return {
					open: this.open,
					searchable: this.searchable,
					loading: this.loading
				}
			},

			/**
			 * Return the placeholder string if it's set
			 * & there is no value selected.
			 * @return {String} Placeholder text
			 */
			searchPlaceholder() {
				if (this.isValueEmpty && this.placeholder) {
					return this.placeholder;
				}
			},

			/**
			 * The currently displayed options, filtered
			 * by the search elements value. If tagging
			 * true, the search text will be prepended
			 * if it doesn't already exist.
			 *
			 * @return {array}
			 */
			filteredOptions() {
				let options = this.$options.filters.filterBy(this.options, this.search)
				if (this.taggable && this.search.length && !this.optionExists(this.search)) {
					options.unshift(this.search)
				}
				return options
			},

			/**
			 * Check if there aren't any options selected.
			 * @return {Boolean}
			 */
			isValueEmpty() {
				if (this.value) {
					if (typeof this.value === 'object') {
						return !Object.keys(this.value).length
					}
					return !this.value.length
				}

				return true;
			},

			/**
			 * Return the current value in array format.
			 * @return {Array}
			 */
			valueAsArray() {
				if (this.multiple) {
					return this.value
				} else if (this.value) {
					return [this.value]
				}

				return []
			}
		}

	}
</script>
