---
layout: post
title:  "Welcome to my Mod blog!"
date:   2016-11-19 20:54:58 +0100
categories: jekyll update
---
You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

<div class="note">
<p>Jekyll also offers powerful support for code snippets:</p>
</div>

### Xml
```xml
  <BodyPartDef>
    <defName>Brain</defName>
    <label>brain</label>
    <hitPoints>10</hitPoints>
    <oldInjuryBaseChance>1</oldInjuryBaseChance>
    <skinCovered>false</skinCovered>
    <activities>
      <li>Consciousness_brain</li>
      <li>Sight_brain</li>
      <li>Hearing_brain</li>
      <li>Moving_brain</li>
      <li>Manipulation_brain</li>
      <li>Talking_brain</li>
    </activities>
    <dontSuggestAmputation>true</dontSuggestAmputation>
  </BodyPartDef>
```

### C\#

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Reflection;

using RimWorld;
using Verse;
using Verse.AI;

namespace CommunityCoreLibrary
{

    [SpecialInjectorSequencer( InjectionSequence.MainLoad, InjectionTiming.SpecialInjectors )]
    public class DetourInjector : SpecialInjector
    {

        public override bool                Inject()
        {
            // Change CompGlower into CompGlowerToggleable
            FixGlowers();

            // Change Building_NutrientPasteDispenser into Building_AdvancedPasteDispenser
            UpgradeNutrientPasteDispensers();

            // Detour RimWorld.MainTabWindow_Research.DrawLeftRect "NotFinished" predicate function
            // Use build number to get the correct predicate function
            var RimWorld_MainTabWindow_Research_DrawLeftRect_NotFinished_Name = string.Empty;
            var RimWorld_Build = RimWorld.VersionControl.CurrentBuild;
            switch( RimWorld_Build )
            {
            case 1279:
                RimWorld_MainTabWindow_Research_DrawLeftRect_NotFinished_Name = "<DrawLeftRect>m__495";
                break;
            case 1284:
                RimWorld_MainTabWindow_Research_DrawLeftRect_NotFinished_Name = "<DrawLeftRect>m__496";
                break;
            default:
                CCL_Log.Trace(
                    Verbosity.Warnings,
                    "CCL needs updating for RimWorld build " + RimWorld_Build.ToString() );
                break;
            }
            if( RimWorld_MainTabWindow_Research_DrawLeftRect_NotFinished_Name != string.Empty )
            {
                MethodInfo RimWorld_MainTabWindow_Research_DrawLeftRect_NotFinished = typeof( RimWorld.MainTabWindow_Research ).GetMethod( RimWorld_MainTabWindow_Research_DrawLeftRect_NotFinished_Name, Controller.Data.UniversalBindingFlags );
                MethodInfo CCL_MainTabWindow_Research_DrawLeftRect_NotFinishedNotLockedOut = typeof( Detour._MainTabWindow_Research ).GetMethod( "_NotFinishedNotLockedOut", Controller.Data.UniversalBindingFlags );
                if( !Detours.TryDetourFromTo( RimWorld_MainTabWindow_Research_DrawLeftRect_NotFinished, CCL_MainTabWindow_Research_DrawLeftRect_NotFinishedNotLockedOut ) )
                    return false;
            }

            /*
            // Detour 
            MethodInfo foo = typeof( foo_class ).GetMethod( "foo_method", Controller.Data.UniversalBindingFlags );
            MethodInfo CCL_bar = typeof( Detour._bar ).GetMethod( "_bar_method", Controller.Data.UniversalBindingFlags );
            if( !Detours.TryDetourFromTo( foo, CCL_bar ) )
                return false;
            */

            return true;
        }

        private void                        FixGlowers()
        {
            foreach( var def in DefDatabase<ThingDef>.AllDefs.Where( def => (
                ( def != null )&&
                ( def.comps != null )&&
                ( def.HasComp( typeof( CompGlower ) ) )
            ) ) )
            {
                var compGlower = def.GetCompProperties<CompProperties_Glower>();
                compGlower.compClass = typeof( CompGlowerToggleable );
            }
        }

        private void                        UpgradeNutrientPasteDispensers()
        {
            foreach( var def in DefDatabase<ThingDef>.AllDefs.Where( def => def.thingClass == typeof( Building_NutrientPasteDispenser ) ) )
            {
                def.thingClass = typeof( Building_AdvancedPasteDispenser );
            }
        }

    }

}
```

*Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].*

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
