<template>
	<div>
		Calendar - Unimplemented!
	</div>
</template>

<script>
	var Router = require("../../libs/router.js");
	var searchDbQuery = require("../../libs/search.js");

	module.exports = {
		props: ["userSettings", "userInfo", "langTranslation"],
		name: "project-calendar",
		data: () => {
			return {
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
            }
		}
	}
</script>