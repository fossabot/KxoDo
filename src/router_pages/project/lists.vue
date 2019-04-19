<template>
	<v-container grid-list-xl>
		<v-layout row wrap fill-height>
			<v-flex xs12 sm8>
				<v-divider></v-divider>
				<v-subheader>Basics</v-subheader>

				<v-menu absolute :position-x="x" :position-y="y" offset-y :close-on-content-click="false" nudge-width="200" v-model="testMenu" max-width="400" lazy> <!-- Do I want this to be lazy? -->
					<component :is="task_popover_card" :user-settings="userSettings" :user-info="userInfo" :langTranslation="langTranslation"></component>
				</v-menu>

				<v-list subheader>
					<v-subheader>Basic Vuetify Layout</v-subheader>
					<v-list-tile avatar :class="{ 'done': test }" @click="showMenu">
						<v-list-tile-action>
						  	<v-checkbox v-model="test" value="false" @click.stop></v-checkbox>
						</v-list-tile-action>
						<v-list-tile-content>
							<v-list-tile-title>Toolbar and Navbar</v-list-tile-title>
						</v-list-tile-content>
					</v-list-tile>

					<v-list-tile avatar>
						<v-list-tile-action>
						  	<v-checkbox value="false"></v-checkbox>
						</v-list-tile-action>
						<v-list-tile-content>
							<v-list-tile-title>Homepage</v-list-tile-title>
						</v-list-tile-content>
					</v-list-tile>

					<v-divider></v-divider>
					<v-subheader>Passwords</v-subheader>
					<v-list-tile avatar>
						<v-list-tile-action>
						  	<v-checkbox value="false"></v-checkbox>
						</v-list-tile-action>
						<v-list-tile-content>
							<v-list-tile-title>Add Password</v-list-tile-title>
						</v-list-tile-content>
					</v-list-tile>
					<v-list-tile avatar>
						<v-list-tile-action>
						  	<v-checkbox value="false"></v-checkbox>
						</v-list-tile-action>
						<v-list-tile-content>
							<v-list-tile-title>Remove Password</v-list-tile-title>
						</v-list-tile-content>
					</v-list-tile>
					<v-list-tile avatar>
						<v-list-tile-action>
						  	<v-checkbox value="false"></v-checkbox>
						</v-list-tile-action>
						<v-list-tile-content>
							<v-list-tile-title>Encryption (ECIES vs. AES)</v-list-tile-title>
						</v-list-tile-content>
					</v-list-tile>
				</v-list>
			</v-flex>
			<v-flex xs12 sm4>
				<v-divider></v-divider>
				<v-subheader>Lists</v-subheader>

				<v-list dense subheader>
					<v-list-tile class="active" @click="">
						<v-list-tile-content>
							<v-list-tile-title>Basics</v-list-tile-title>
						</v-list-tile-content>
					</v-list-tile>
					<v-list-tile @click="">
						<v-list-tile-content>
							<v-list-tile-title>Extra</v-list-tile-title>
						</v-list-tile-content>
					</v-list-tile>
					<v-list-tile @click="">
						<v-list-tile-content>
							<v-list-tile-title>Files</v-list-tile-title>
						</v-list-tile-content>
					</v-list-tile>
				</v-list>
			</v-flex>
		</v-layout>
	</v-container>
</template>

<style lang="css" scoped>
    .active {
        background-color: #D3D3D3;
    }
    .theme--dark .active {
        background-color: #505050;
    }
    .done {
        background-color: #E3E3E3;
    }
    .theme--dark .done {
        background-color: #505050;
    }
</style>

<script>
	var Router = require("../../libs/router.js");
	var searchDbQuery = require("../../libs/search.js");
	var TaskPopoverCard = require("../../vue_components/task_popover_card.vue");

	module.exports = {
		props: ["userSettings", "userInfo", "langTranslation"],
		name: "project-lists",
		data: () => {
			return {
				test: false,
				testMenu: false,
				x: 0,
				y: 0,
				task_popover_card: TaskPopoverCard
			};
		},
		beforeMount: function() {
			var self = this;

			this.$emit("setcallback", "update", function(userInfo) {
			});
		},
		computed: {
			isLoggedIn: function() {
				if (this.userInfo == null) return false;
				return this.userInfo.cert_user_id != null;
			}
		},
		methods: {
			goto: function(to) {
				Router.navigate(to);
			},
			login: function() {
				page.selectUser();
				return false;
			},
			gotoLink: function(to) {
				console.log(to);
				window.location = to;
            },
            showMenu: function(e) {
            	e.preventDefault()
		        this.testMenu = false
		        this.x = e.clientX
		        this.y = e.clientY
		        this.$nextTick(() => {
		          this.testMenu = true
		        })
            }
		}
	}
</script>