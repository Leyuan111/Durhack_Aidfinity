<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="scss">
	/* Make sure to place this on the first line, and add the "lang="scss"" attribute to the style tag */
	@import "uview-ui/index.scss";
	page{
		font-size: 28rpx;
		overflow-y: hidden;
	}
</style>