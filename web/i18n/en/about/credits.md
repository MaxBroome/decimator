<script lang="ts">
    import { contacts, docs } from "$lib/env";
    import { t } from "$lib/i18n/translations";

    import SectionHeading from "$components/misc/SectionHeading.svelte";
    import BetaTesters from "$components/misc/BetaTesters.svelte";
</script>

<section id="imput">
<SectionHeading
    title="imput"
    sectionId="imput"
/>

decimator is made with love and care by the [imput](https://imput.net/) research and development team.

you can support us on the [donate page](/donate)!
</section>

<section id="testers">
<SectionHeading
    title={$t("about.heading.testers")}
    sectionId="testers"
/>

huge shoutout to our thing breakers for testing updates early and making sure they're stable.
they also helped us ship decimator 10!
<BetaTesters />

all links are external and lead to their personal websites or social media.
</section>

<section id="meowbalt">
<SectionHeading
    title={$t("general.meowbalt")}
    sectionId="meowbalt"
/>

meowbalt is decimator's speedy mascot. he is an extremely expressive cat that loves fast internet.

all amazing drawings of meowbalt that you see in decimator were made by [GlitchyPSI](https://glitchypsi.xyz/).
he is also the original designer of the character.

you cannot use or modify GlitchyPSI's artworks of meowbalt without his explicit permission.

you cannot use or modify the meowbalt character design commercially or in any form that isn't fan art.
</section>

<section id="licenses">
<SectionHeading
    title={$t("about.heading.licenses")}
    sectionId="licenses"
/>

decimator processing server is open source and licensed under [AGPL-3.0]({docs.apiLicense}).

decimator frontend is [source first](https://sourcefirst.com/) and licensed under [CC-BY-NC-SA 4.0]({docs.webLicense}).
we decided to use this license to stop grifters from profiting off our work
& from creating malicious clones that deceive people and hurt our public identity.

we rely on many open source libraries, create & distribute our own.
you can see the full list of dependencies on [github]({contacts.github}).
</section>
