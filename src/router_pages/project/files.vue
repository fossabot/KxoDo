<template>
	<v-container grid-list-xl>
		<v-layout row wrap fill-height>
			<v-flex xs12 sm7>
				<v-divider></v-divider>
				<v-subheader>Files</v-subheader>

				<v-list>
					<template v-for="(file, index) in files">
						<v-list-tile :key="file.auth_address + '_' + file.id" avatar :class="{ 'active': activeFileIndex == index }" @click="showFile(index)">
							<v-list-tile-content>
								<v-list-tile-title>{{ file.title }}</v-list-tile-title>
							</v-list-tile-content>
						</v-list-tile>
					</template>
				</v-list>
			</v-flex>
			<v-flex xs12 sm5>
				<v-tabs-items v-model="activeFile">
					<v-tab-item key="0">
						<v-divider></v-divider>
						<v-subheader>Filter and Sort</v-subheader>
						<v-text-field prepend-icon="search" placeholder="Search" v-model="searchQuery"></v-text-field>
					</v-tab-item>
					<v-tab-item v-for="(file, index) in files" :key="index" lazy>
						<div v-if="activeFileIndex != null">
							<v-divider></v-divider>
							<v-subheader>{{ file.title }}</v-subheader>
							<div style="padding-left: 16px; padding-right: 16px;">
								<span v-html="getMarkdown(file.description)"></span>
								<v-btn flat color="blue">Download</v-btn>
							</div>

							<v-divider style="margin-top: 15px;"></v-divider>
							<v-subheader>Conversations Assigned To</v-subheader>
							<div style="padding-left: 16px; padding-right: 16px;">
							</div>
						</div>
					</v-tab-item>
				</v-tabs-items>
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
	.markdown p {
		margin-bottom: 8px !important;
	}
</style>

<script>
	var Router = require("../../libs/router.js");
	var searchDbQuery = require("../../libs/search.js");

	module.exports = {
		props: ["userSettings", "userInfo", "langTranslation"],
		name: "project-files",
		data: () => {
			return {
				files: [
					{ title: "Prerelease #1", description: "This is a zip of all of the code of the very first prerelease.", id: 111, auth_address: "testAuth", cert_user_id: "kxobot@kxoid.bit" }
				],
				activeFile: "0",
				activeFileIndex: null
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
			getMarkdown: function(data) {
				return window.md.render(data);
			},
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
			showFile: function(index) {
				const isActive = parseInt(this.activeFile) != 0;
				if (isActive) {
					if (this.activeFileIndex == index) {
						this.activeFile = (0).toString();
						var self = this;
						setTimeout(function() {
							self.activeFileIndex = null;
						}, 400);
					} else {
						this.activeFileIndex = index;
						this.activeFile = (index + 1).toString();
					}
				} else {
					this.activeFileIndex = index;
					this.activeFile = (index + 1).toString();
				}
			}
		}
	}
</script>