<template>
    <div>
        <h2>Kontakt</h2>
        <div class="text-container">
            <p>
                Om du har frågor går det jättebra att kontakta oss genom
                antingen e-post eller telefon. Vi finns tillgängliga under
                våra öppettider.
            </p>
            <h3></h3>
            <div class="open">
                <div>Mån-fre kl. 09:00-17:00</div>
                <div>Lör-sön kl. 11:00-16:00</div>
            </div>
        </div>
        {{ this.msg }}
    </div>
</template>

<script>
export default {
    data() {
        return {
            msg: "hej"
        }
    }
}
</script>

<style>
</style>