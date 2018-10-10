<h1>lysla's Vue.js cheatsheet</h1>
<p>Simple indexed manual with notes.</p>

# structure

<em>General Vue instance or component structure</em>

```js
{
	data() {
		return {
			myData: 'some data',
			//...
		}
	},
	computed: {
		myComputedData() {

		},
		//...
	}
	methods: {
		myMethod() {

		},
		//...
	},
	watch: {
		dataToWatch: function() {
			// do something when data changes
		}
	}
	//...
}
```

## common initial instance

<em><small>In main js file (main.js)</small></em>
```js
import Vue from 'vue';
import App from './..path../App.vue';

new Vue({
	el: '#app',
	render: h => h(App)
});
```
<em><small>In main html file</small></em>
```html
<html>
	<!-- ... -->
	<div id="app"></div>
	<!-- ... -->

	<script src="..path../compiled.js"></script>
</html>
```


# built-in directives

## v-if and v-show
<em>Renders an element only if the condition is true</em>
```html
<div v-if="condition == true"></div>
<!-- also -->
<div v-if="condition == true"></div>
<div v-else></div>
```
<em>Show an element (that is rendered anyway) only if the condition is true</em>
```html
<div v-show="condition == true"></div>
```

### v-for
<em>Loop throu a cycle and renders an element each time</em>
```html
<div v-for="n in array">{{ n }}</div>
```
<em><small>Current index is available</small></em>
```html
<div v-for="(n, i) in array">{{ n }} is the element number {{ i }}</div>

```

### v-bind and v-on
<em>Both directives are commonly short-cut, relatively to : and @</em>

<em><small>v-bind example</small></em>
```html
<div class="container any-class" :class="someData"></div>

<script>
	//...
	data() {
		return {
			someData: 'dynamic-class'
		}
	}
</script>

```
<em><small>v-on example</small></em>
```html
<button @click="myMethod"></button>
<a href="#" @hover="myMethodHover"></a>
<script>
	//...
	methods: {
		myMethod() {
			// do something on click event
		},
		myMethodHover() {
			// do something on hover event
		}
	}
</script>

```


## components

### general

<em>Importing components in main Vue instance</em>

<em><small>Vue instance (App.vue)</small></em>
```html
<comp-name></comp-name>
...
<script>
	import CompName from './..path../CompName.vue';

	export default {
		...
		components: {
			'comp-name': CompName //or compName: CompName
		}
	}
</script>
```
<em><small>Vue component (CompName.vue)</small></em>
```html
<template>
	<div>
		...
	</div>
</template>
<script>
	export default {
		//...
	}
</script>
```
### dynamic components

```html
<component :is="someVariableData">
	<!-- can hold default content -->
	default content
</component>
```
<em>Prevent destroying components when switching between them</em>
```html
<keep-alive>
	<component :is="someVariableData"></component>
</keep-alive>
```

### props 

<em>Passing data from component to child component</em>

<em><small>Parent component</small></em>
```html
<child-component :name="someData"></child-component>
<script>	
	data() {
		return {
			someData: 'some data'
		}
	}
	//...
</script>
```
<em><small>Child component</small></em>
```html
<template>
	{{ someData }}
</template>
<script>
	//...
	props: ['someData']
	//or
	props: {
		someData: {
			type: SomeType //String, Array...
			required: false,
			default: 'some default data'
		}
	}
</script>
```
### slots
<em>Reserving html space in child components</em>
<em><small>Parent</small></em>
```html
<child-component>
	<div></div>
	<div slot="partialSlot"></div>
</child-componenet>
```
<em><small>Child component</small></em>
```html
<template>
	<div>
		<!-- this slot will render all html inside the component tag in the parent which does not have a slot attribute (slot name) -->
		<slot></slot>

		<!-- will render only html which has the corresponding name -->
		<slot name="partialSlot"></slot>

		<!-- slots can have a default value when they don't find any html to render -->
		<slot name="missingSlot">
			some default html
		</slot>
	</div>
</template>
```

## events

<em>Listening to custom events and how to emit them</em>

<em><small>Component in the listener Vue file</small></em>
```html
<my-component @myCustomEvent="someMethod($event)"></my-component>

<script>
	//...
	methods: {
		someMethod(someArgs) {

		}
	}	
</script>
```
<em><small>Component which emits the custom event</small></em>
```html
<button @click="myMethod(someArgs)"></button>

<script>
	//...
	myMethod() {
		this.$emit('myCustomEvent', someArgs);
	}
</script>
```

## filters

🎈 **filters can be used as a handy alternative to computed proprieties**

<em>Usage</em>

<em><small>Filtering data before displaying it</small></em>
```html
<p>{{ myData | myfilter }}</p>
<!-- or multiple filters -->
<p>{{ myData | myfilter | anotherfilter }}</p>
```

<em>Global filter example</em>

<em><small>In main js file (main.js)</small></em>
```js
Vue.filter('myfilter', function(value) {
	// value is the value to filter somehow
	return value;
});
```

<em>Local filter example</em>

<em><small>In the component</small></em>
```html
<script>
	export default {
		//...
		filters: {
			myfilter(value) {
				// do something with value
				return value;				
			}
		}
	}
</script>
```

## mixins

<p>Snippet of structured code to re-use in multiple application parts</p>

<em>Mixins live in separated files</em>

🎈 **data values aren't shared, they just get copy-pasted**

🎈 **mixins don't override nor destroy existing proprieties**

<em><small>In myMixin.js</small></em>
```js
export const myMixin = {
	data() {
		return {
			//...
		}
	},
	methods: {
		//...
	},
	computed: {
		//...
	},
	//...
}

```

<em><small>In Vue instance or component where i need the mixin</small></em>
```html
<script>
	import { myMixin } from './..path../myMixin';

	export default {
		mixins: [myMixin],
		//...
	}
</script>
```


# forms 

## v-model

### input text

<em>Automatically update the data everytime the input value changes</em>
```html
<form>
	<input type="text" id="name" v-model="myData.name">
</form>

<script>
	//...
	data() {
		return {
			myData: {
				name: 'Name',
				email: 'email@email.com'
			}
		}
	}
</script>
```

<em>Change when to update the value, rather then the default 'change' event, to the 'out of focus' event</em>

```html
<input type="text" id="name" v-model.lazy="myData.name">
```

### checkbox and radio

<em>Automatically store values with the same model into an array</em>

```html
<input type="checkbox" id="cb1" v-model="arrayData">
<input type="checkbox" id="cb2" v-model="arrayData">

<script>
	//...
	data() {
		return {
			arrayData: []
		}
	}
</script>

```
<em>Radio with the same model are automatically bound to the same radio group.</em>

```html
<input type="radio" id="rb1" value="1" v-model="radioValue">
<input type="radio" id="rb2" value="2" v-model="radioValue">

<script>
	//...
	data() {
		return {
			radioValue: 1 // default or empty value
		}
	}
</script>

```

### select

<em>Use for cycle to render available options, and a data variable to store the selected value</em>

```html
<select id="mySelect" v-model="selectValue">
	<option v-for="n in myArray">{{ n }}</option>
</select>

<script>
	//...
	data() {
		return {
			myArray: ['1', '2', '3'],
			selectValue: '1' // default or empty value
		}
	}
</script>
```

# custom directives


<em>Usage</em>

<em><small>With value passed, which can be hardcoded or any data value or object</small></em>
```html
<p v-somename="'someString'"></p>
<!-- or -->
<p v-somename="someData"></p>
```

<em><small>With value and arg passed</small></em>
```html
<p v-somename:somearg="someData"></p>
```

<em><small>With value, arg and modifiers passed</small></em>
```html
<p v-somename:somearg.somemod.othermod="someData"></p>
```

<em>Global directive example</em>

<em><small>In main js file (main.js)</small></em>
```js
Vue.directive('somename', {
	bind(el, binding, vnode) {
		// el is the html element
		// binding.value is the value passed
		// binding.arg contains the arg passed
		// binding.modifiers[] containers all modifiers passed, accessible by their name as array key
	}
});
```

<em>Local directive example</em>

<em><small>In the component</small></em>
```html
<script>
	export default {
		//...
		directives: {
			'somename': {
				bind(el, binding, vnode) {
					// do something
				}
			}
		}
	}
</script>
```

# hooks

## components life cycle

`beforeCreate()`

`created()`

`beforeUpdate()`

`beforeMount()`

`mounted()`

`updated()`

`beforeDestroy()`

`destroyed()`

<em>For `<keep-alive>` dynamic components only</em>

`activated()`

`deactivated()`

## directives

`bind(el, binding, vnode)`

`inserted(el, binding, vnode)`

`update(el, binding, vnode, oldVnode)`

`componentUpdated(el, binding, vnode, oldVnode)`

`unbind(el, binding, vnode)`

## transitions

`before-enter()`

`enter()`

`after-enter()`

`after-enter-cancelled()`

`before-leave()`

`leave()`

`after-leave()`

`after-leave-cancelled()`

## routing

<small>In main.js</small>

`beforeEach(to, from, next)`

<small>In routes.js</small>

`beforeEnter(to, from, next)`

<small>In component</small>

`beforeRouteEnter(to, from, next)`

<small>In component only</small>

`beforeRouteLeave(to, from, next)`


# transitions

```html
<transition name="somename">
	<some-component-or-html-single-el>
	</some-component-or-html-single-el>
	<!-- or -->
	<some-comp-or-html v-if="" key="k1"></some-comp-or-html>
	<some-comp-or-html v-else key="k2"></some-comp-or-html>
	<!-- or -->
	<my-dynamic-component :is="someData"></my-dynamic-component>
	<!-- or -->
	<router-view></router-view>
</transition>
```

🎈 **key propriety is needed in transitioning v-if elements**

<em>Trigger animation on the first rendering on page loaded</em>
```html
<transition appear>
	<!-- --->
</transition>
```

<em>Change how animation works when used to switch between componenets</em>
```html
<transition mode="out-in"></transition>
<transition mode="in-out"></transition>
```

<em>Custom classes names</em>
```html
<transition 
	enter-class="my-enter"
	enter-active-active="my-enter-active"
	leave-class="my-leave"
	leave-active-class="my-leave-active"
	move-class="my-move">
</transition>
```

<em>Transition group</em>

🎈 **differently from single transition, this renders a default span which contain all the html, can be overwritten by setting the propriety tag="someothertag"**

```html
<ul> <!-- or any kind of element list -->
	<transition-group move-class="my-move">
		<!-- key propriety is mandatory -->
		<li key="k1"></li>
		<li key="k2"></li>
		<li key="k3"></li>
	</transition-group>
</ul>
```

<em>Generated classes</em>

<em><small>When transition has the name propriety, this classes get automatically generated with that name</small></em>

🎈 **css animations work better with keyframes**

<em><small>css animation example</small></em>
```css
.somename-enter {

}
.somename-enter-active {
	animation: animation-in 300ms ease-in forwards;
}
.somename-leave {

}
.somename-leave-active {
	animation: animation-out 300ms ease-out forwards;
	/** hack for transition-group animation when an element is removed **/
	position: absolute;
}
/** specifically for transition-group **/
.somename-move {
	transition: all 300ms ease-in;
}
/*css keyframe animation example*/
@keyframes animation-in {
	from: {
		opacity: 0;
	}
	to: {
		opacity: 1;
	}
}
@keyframes animation-out {
	from: {
		opacity: 1;
	}
	to: {
		opacity: 0;
	}
}
```

<em>Listening to animation hooks</em>

```html
<transition
	@before-enter="beforeEnterMethod"
	@enter="enterMethod">
	<!-- transitioning element --->
</transition>

<script>
	export default {
		//...
		methods: {
			beforeEnterMethod(el) {
				// el is the transitioning element
			},
			enter(el, done) {
				// done must be called when using custom hooks without css animations
				done();
			}
		}
	}
</script>
```

# routing

<em>Vue.js manage routing via vue-router which needs to be installed</em>

<em><small>In main js file (main.js)</small></em>
```js
import Vue from 'vue';
import VueRouter from 'vue-router';
import App from './..path../App.vue';

/* routes.js file */
import { routes } from './..path../routes';

Vue.use(VueRouter);
const router = new VueRouter({
	routes,
	mode: 'hash' // or history
	// 🎈 history will only work if the server is configurated so it always return index.html even with 404 error
});

new Vue({
	el: '#app',
	router,
	render: h => h(App)
});

//...
```

<em><small>Create routes.js file</small></em>
```js
import PageHome from './..path../PageHome.vue';
import PageFirst from './..path../PageFirst.vue';
import PageSecond from './..path../PageSecond.vue';

export const routes = [
	{
		path: '',
		component: PageHome
	},
	{
		path: '/page1',
		component: PageFirst,
		name: 'someRouteName' // not mandatory but useful
	},
	{
		path: '/page2',
		componenent: PageSecond
	}
]
```

<em><small>App.vue main instance</small></em>
```html
<template>
	<div>
		<!-- ... --->
		<router-view>
			<!-- routed components will be rendered here --->
		</router-view>
	</div>
</template>

```

## routing parameters

<em><small>In routes.js</small></em>
```js
import PageHome from './..path../User.vue';

export const routes = [
	{
		path: '/user/:id',
		component: User,
		name: 'someRouteName'
	}
]
```

<em><small>In User component (User.vue)</small></em>
```html
<template>
	<div>
		Url parameter: {{ id }}
	</div>
</template>

<script>
	export default {
		data() {
			return {
				id: this.$route.params.id
			}
		},
		// 🎈 if we have a way to reactivate this component without destroying and recreating it, we need to watch the route to be updated everytime it changes, otherwise this is not needed
		watch: {
			'$route'(to, from) {
				this.id = to.params.id;
			}
		}
	}
</script>
```

## routing links

<em>Simple examples</em>

```html
<router-link to="/page">My page link</router-link>
<router-link to="/">My home link</router-link>
<router-link :to="someDataLink">My dynamic link</router-link>
```

<em>Active class and html style example</em>

<em><small>We want a li tag which will contain an a tag, and the active class will be added only with url exact match</small></em>
```html
<router-link to="/" tag="li" active-class="my-active-class" exact><a>Home</a></router-link>
```

<em>Trigger navigation via javascript</em>

```js
//...
export default {
	//...
	methods: {
		navigateToPage() {
			this.$router.push('/page1');
			// or
			this.$router.push( { name: 'someRouteName' });
		}
	}
}

```

<em>Dynamic link with parameters example, taking the current route id</em>
```html
<router-link :to="myLink">My Link</router-link>

<script>
	export default {
		data() {
			return {
				myLink: { 
					name: 'someRouteName', 
					params: { id: $route.params.id } 
				},
				// we can also pass query data
				myLinkWithQuery: {
					name: 'someRouteName', 
					query: { lang: 'eng' }
				},
				// and hash
				myLinkWithHash: {
					name: 'someRouteName',
					hash: '#someHash'
				}
			}
		}
	}
</script>
```

## nested routes

<em>We can have router views inside components already rendered inside a 'root' router view</em>

<em><small>In the parent we do likewise as non-nested routes</small></em>
```html
<template>
	<div>
		<router-view></router-view>
	</div>
</template>
```

<em><small>In routes.js</small></em>
```js
// ... components imports

export const routes = [
	{
		path: '/user',
		component: User,
		children: [
			{
				path: 'new', // /user/new
				component: 'UserNew.vue'
			},
			{
				path: ':id', // user/123
				component: 'UserDetail.vue'
			},
			{
				path: ':id/edit', //user/123/edit
				component: 'UserEdit.vue'
			}
		]
	}
]
```

## multiple router views

<em>You can change rendering place of components relatively to the router view</em>

<em><small>In the component</small></em>
```html
<template>
	<div>
		<router-view></router-view>
		<router-view name="position-one"></router-view>
		<router-view name="position-two"></router-view>
	</div>
</template>
```

<em><small>In routes.js</small></em>
```js
// ... components imports

export const routes = [
	{
		path: '/page',
		components: {
			default: MainComponent,
			positionOne: SwitchingComponent
		}
	},
	{
		path: '/pageSwitched',
		components: {
			default: MainComponent,
			positionTwo: SwitchingComponent
		}
	}
]
```

## redirects

<em><small>In routes.js</small></em>
```js
export const routes = [
	{
		path: '/nowhere',
		redirect: '/gosomewhereelse'
	},
	// also
	{
		path: '/home',
		name: 'routeHome'
	},
	{
		path: '/oldhome',
		redirect: { name: 'routeHome' }
	},
	// redirect all non-managed paths
	{
		path: '*',
		redirect: { name: 'routeHome' }
	}
]
```

## guarding navigation hooks


<em><small>Definition in component</small></em>
```html
<script>
	export default {
		//...
		beforeRouteEnter(to, from, next) {
			// call to proceed
			next();
		},
		beforeRouteLeave(to, from, next) {
			// call to proceed
			next();
		}
	}
</script>
```
<em><small>Definition in routes.js (uncommon)</small></em>
```js
export const routes = [
	{
		path: '/somewhere',
		component: SomeComponent,
		beforeEnter: (to, from, next) => {
			// call to proceed
			next();
		}
	}
]
```

# lazy loading (webpack)

<em>Importing components with lazy load for a webpack configuration</em>

```js
//import MyComponent from './..path../MyComponent.vue';

const MyComponent = resolve => {
	require.ensure(['./..path../MyComponent.vue'], () => {
		resolve(require('./..path../MyComponent.vue'));
	}, 'bundleGroupName');
};
```
🎈 **bundleGroupName is not mandatory and but useful to let webpack bundle multiple components together even if lazy loaded**