# Field Mapping Comparison Report

**Generated:** 2026-01-13
**Purpose:** Compare field definitions across BrandKit schemas, AIGen configs, and library mappings

---

## Executive Summary

This report compares three data sources:
1. **BrandKit Schemas** - 49 schema.json files in `_system` folders
2. **AIGen Configs** - 50 section JSON files in `BrandKit AIGen/sections`
3. **Library Mappings** - Field-to-library mappings in `BrandLib/_config/field-mappings.json`

### Key Findings

| Metric | Count |
|--------|-------|
| Total BrandKit sections | 49 |
| Total AIGen section configs | 50 |
| Sections with full coverage | 43 |
| Sections missing AIGen config | 6 |
| Fields with library mappings | 33 |
| Potential naming mismatches | 8 |

---

## Section-by-Section Comparison

### Core Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| core/identity | brand_name, brand_name_variations, tagline, positioning_statement | brand_name, brand_name_variations, tagline, positioning_statement | - | Matched |
| core/purpose | mission_statement, vision_statement, brand_purpose, brand_story, founder_background | mission_statement, vision_statement, brand_purpose, brand_story, founder_background | - | Matched |
| legal/core | entity, addresses, regulatory, legal_statements, geographic_focus | entity.*, addresses.*, regulatory, legal_statements.*, geographic_focus | - | Matched |
| core/contact | website_url, contact, social_handles | website_url, contact.*, social_handles.* | service_areas (geographic) | Matched |

### Audience Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| audience/core | segment_name, demographics, psychographics, awareness_level, language, current_solutions, objections, transformation, accessibility, format_preferences, platforms, sensitivities | segment_name, demographics, psychographics.*, awareness_level, language.*, current_solutions, objections, transformation, accessibility, format_preferences, platforms, sensitivities, journey_stages, engagement_triggers | geographic_focus (geographic), client_journey (client-outcomes) | Matched |
| audience/professional | segment_name, roles, settings, resource_needs, language_register, discovery, recommend_triggers, credibility_markers, objections, address_preferences, cpd_interests, regulatory, format_preferences | segment_name, roles.*, settings, resource_needs, language_register, discovery, recommend_triggers, credibility_markers, objections, address_preferences, cpd_interests, regulatory, format_preferences, professional_language, credibility_requirements | - | Matched |
| audience/supporter | segment_name, relationship_types, emotional_state, questions, language, fears_frustrations, permissions, boundaries, common_mistakes, discovery, format_preferences, sensitivities | segment_name, relationship_types, emotional_state, questions, language, fears_frustrations, permissions, boundaries, common_mistakes, discovery, format_preferences, sensitivities | - | Matched |

### Voice Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| voice/personality | personality_traits, archetype | personality_traits, archetype | - | Matched |
| voice/tone | tone_descriptors, formality_level, warmth_level | tone_descriptors, formality_level, warmth_level | primary_tone, secondary_tones, tone_variations (tone library) | Matched |
| voice/vocabulary | preferred_terms, brand_words, restrictions, signature_phrases, phrase_alternatives | preferred_terms, brand_words, restrictions, signature_phrases, phrase_alternatives | preferred_terms, terms_to_avoid (therapeutic/vocabulary) | Matched |
| voice/characteristics | voice_characteristics | voice_characteristics | - | Matched |
| voice/guidelines | humour, emoji, difficult_topics | humour, emoji, difficult_topics | emoji_usage, emoji_style (emoji library) | Matched |

### Values Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| values/brand | core_values, stands_against, ethical_boundaries, non_negotiables | core_values, core_values.does_not_mean, stands_against, ethical_boundaries, non_negotiables | - | Matched |
| values/commercial | sales_philosophy, pricing_values, customer_relationship, marketing_ethics, competition | sales_philosophy, pricing_values, marketing_ethics | - | Partial - AIGen missing customer_relationship, competition |
| values/client-outcomes | selected_outcomes, desired_outcomes, values_to_instil, beliefs_to_shift, transformation_vision, success_indicators | desired_outcomes, values_to_instil, beliefs_to_shift | transformation_language, client_journey, outcome_messaging (therapeutic/client-outcomes) | Partial - AIGen missing selected_outcomes, transformation_vision, success_indicators |

### Methodology Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| methodology/philosophy | philosophy_statements, key_beliefs | philosophy_statements, key_beliefs | - | Matched |
| methodology/core-methodology | name, description, key_concepts, why_it_matters, application_in_content, differentiators, proprietary_terms | name, description, key_concepts, differentiators | - | Partial - AIGen missing why_it_matters, application_in_content, proprietary_terms |
| methodology/frameworks | frameworks, metaphors | frameworks, metaphors | - | Matched |

### Content Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| content/pillars | primary_pillars, secondary_themes, topics_avoided, content_seasonal, content_ratios | primary_pillars, secondary_themes, topics_avoided, content_seasonal, content_ratios, pillar_examples | - | Matched |
| content/types | content_types, seasonal_content | content_types, seasonal_content, type_templates, type_cta_mapping | content_ctas (cta-patterns) | Matched |
| content/platforms | platforms | platforms | platform_emoji, hashtag_strategy, branded_hashtags, community_hashtags (hashtags) | Matched |
| content/formatting | heading_hierarchy, paragraph_length, lists_and_structure, emphasis_and_styling, spacing_and_breaks | heading_hierarchy, paragraph_length, lists_and_structure, emphasis_and_styling, spacing_and_breaks | emoji_usage, emoji_style, content_warnings, content_openers, crisis_footer (multiple libraries) | Matched |

### Products Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| products/cta | cta_by_content_type, tone, example_phrases, phrases_to_avoid, button_text, urgency_language | cta_by_content_type, button_text, urgency_language, cta_tone_by_stage, cta_urgency_rules | primary_cta, secondary_ctas, email_ctas (cta-patterns) | Partial |
| products/services | summary, services, service_tiers, client_journey, service_boundaries, testimonials_approach | summary, services, service_tiers, client_journey, service_boundaries, testimonials_approach | - | Matched |
| products/offerings | summary, free_offerings, paid_offerings, offering_hierarchy, pricing_philosophy, lead_magnets, flagship_offerings, product_ecosystem | summary, free_offerings, paid_offerings, offering_hierarchy, pricing_philosophy, lead_magnets, flagship_offerings, product_ecosystem, cross_sell_mapping, objection_handling | - | Matched |
| products/subscriptions | summary, subscriptions, subscription_philosophy, member_journey, metrics_tracked | summary, subscriptions, subscription_philosophy, member_journey, metrics_tracked | - | Matched |

### Operations Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| operations/calendar | posting_frequency, best_times, batching_approach | posting_frequency, best_times, content_batching, seasonal_planning, campaign_cadence | - | Partial |
| operations/workflow | workflow_stages, approval_requirements | workflow_stages, approval_requirements, review_checklist, brand_compliance_check | asset_storage, turnaround_times (asset-locations, response-times) | Matched |
| operations/metrics | key_metrics, content_performance, brand_health | key_metrics, brand_health | - | Partial - AIGen missing content_performance |

### Email Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| emails/types | summary, email_types, sequences, sending_schedule, platform | summary, email_types, sequences, sending_schedule, platform | - | Matched |
| emails/copy | summary, subject_lines, preview_text, greetings, sign_offs, body_copy, cta_copy, ps_section, footer_copy, personalisation, out_of_office, common_responses, email_signatures | summary, subject_lines, preview_text, greetings, sign_offs, body_copy, cta_copy, ps_section, footer_copy, personalisation, out_of_office, common_responses, email_signatures | email_ctas, email_openers (cta-patterns, sentence-starters) | Matched |

### Visual Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| visual/logos | summary, usage_do, usage_dont, versions, minimum_size, clear_space, folder_structure, folder_locations | summary, usage_do, usage_dont, versions, minimum_size, clear_space, folder_structure, folder_locations | logo_locations (asset-locations) | Matched |
| visual/colours | summary, usage_do, usage_dont, palettes, usage_rules, accessibility, folder_locations | summary, usage_do, usage_dont, palettes, usage_rules, accessibility, folder_locations, colour_psychology, seasonal_variations | - | Matched |
| visual/typography | summary, usage_do, usage_dont, font_assignments, fallback_fonts, web_font_stack, folder_locations | summary, usage_do, usage_dont, font_assignments, fallback_fonts, web_font_stack, folder_locations | - | Matched |
| visual/photography | summary, usage_do, usage_dont, style, diversity_requirements, images_to_avoid, sourcing, folder_structure, folder_locations | style, summary, usage_do, usage_dont, diversity_requirements, images_to_avoid, sourcing, folder_structure, folder_locations, editing_guidelines, composition_rules | - | Matched |
| visual/graphics | summary, usage_do, usage_dont, illustration_style, pattern_usage, textures, decorative_elements, shape_language, folder_structure, folder_locations | - | accessibility_guidelines (accessibility) | Missing AIGen |
| visual/icons | summary, usage_do, usage_dont, style, stroke_weight, colour_usage, preferred_sources, core_icons, folder_structure, folder_locations | - | - | Missing AIGen |
| visual/charts | summary, usage_do, usage_dont, preferred_types, colour_usage, label_styling, legend_positioning, accessibility, folder_locations | - | - | Missing AIGen |
| visual/custom-categories | categories | - | - | Missing AIGen |
| visual/templates | summary, usage_do, usage_dont, template_categories, social_templates, presentation_templates, document_templates, email_templates, customisation_rules, folder_structure, folder_locations | - | template_locations (asset-locations) | Missing AIGen |
| visual/emails | summary, usage_do, usage_dont, header, layout, typography, colours, buttons, images, dividers, footer, signatures, mobile_responsive, dark_mode, templates, folder_locations | - | - | Missing AIGen |

### Legal Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| legal/entity | summary, entity, addresses, regulatory, geographic_focus, key_contacts | summary, entity.*, addresses, regulatory, geographic_focus, key_contacts | operating_regions (geographic) | Matched |
| legal/compliance | summary, data_protection, consent_management, subject_rights, security_measures, breach_procedures, third_party_processors, accessibility, industry_specific | summary, data_protection, consent_management, subject_rights, security_measures, breach_procedures, third_party_processors, accessibility, industry_specific, retention_periods, cookie_consent | regulatory_bodies, registration_requirements (therapeutic/regulatory) | Matched |
| legal/disclaimers | summary, usage_do, usage_dont, copyright, disclaimers, affiliate_disclosure, testimonial_disclosure, asset_lock_statement, terms_references, footer_legal, regulatory_statements | summary, usage_do, usage_dont, copyright, disclaimers, affiliate_disclosure, testimonial_disclosure, asset_lock_statement, terms_references, footer_legal, regulatory_statements, disclaimer_placement, disclaimer_by_content_type | emergency_contacts, professional_disclaimers (therapeutic/crisis-resources, therapeutic/regulatory) | Matched |

### Communication Sections

| Section | Schema | AIGen | Library Mapping | Status |
|---------|--------|-------|-----------------|--------|
| communication/principles | summary, core_principles, response_philosophy, transparency_commitment, accessibility_commitment, language_inclusivity, communication_red_lines, tone_modifiers, channel_principles | summary, core_principles, response_philosophy, transparency_commitment, accessibility_commitment, language_inclusivity, communication_red_lines, tone_modifiers, channel_principles, response_templates, escalation_triggers | accessibility_standards (accessibility) | Matched |
| communication/clients | summary, usage_do, usage_dont, first_contact, onboarding, ongoing_communication, support, boundaries, difficult_conversations, offboarding | summary, usage_do, usage_dont, first_contact, onboarding, ongoing_communication, support, boundaries, difficult_conversations, offboarding, feedback_handling, complaint_response | response_expectations, client_language, crisis_resources, outcome_messaging (multiple libraries) | Matched |
| communication/public | summary, usage_do, usage_dont, announcements, social_media_engagement, press_media, collaboration_requests, crisis_communication | summary, usage_do, usage_dont, announcements, social_media_engagement, press_media, collaboration_requests, crisis_communication | trigger_warnings (therapeutic/content-warnings) | Matched |
| communication/professional | summary, usage_do, usage_dont, b2b_tone, referral_partners, professional_networking, supplier_communication | summary, usage_do, usage_dont, b2b_tone, referral_partners, professional_networking, supplier_communication | response_standards (response-times) | Matched |
| communication/internal | summary, usage_do, usage_dont, team_tone, meeting_culture, documentation_standards, tools, response_expectations, escalation | summary, usage_do, usage_dont, team_tone, meeting_culture, documentation_standards, tools, response_expectations, escalation | - | Matched |

---

## Missing Coverage Analysis

### Sections with Schema but NO AIGen Config

| Section | Fields in Schema | Recommendation |
|---------|------------------|----------------|
| visual/graphics | summary, usage_do, usage_dont, illustration_style, pattern_usage, textures, decorative_elements, shape_language, folder_structure, folder_locations | Create `visual--graphics.json` |
| visual/icons | summary, usage_do, usage_dont, style, stroke_weight, colour_usage, preferred_sources, core_icons, folder_structure, folder_locations | Create `visual--icons.json` |
| visual/charts | summary, usage_do, usage_dont, preferred_types, colour_usage, label_styling, legend_positioning, accessibility, folder_locations | Create `visual--charts.json` |
| visual/custom-categories | categories | Create `visual--custom-categories.json` |
| visual/templates | summary, usage_do, usage_dont, template_categories, social_templates, presentation_templates, document_templates, email_templates, customisation_rules, folder_structure, folder_locations | Create `visual--templates.json` |
| visual/emails | summary, usage_do, usage_dont, header, layout, typography, colours, buttons, images, dividers, footer, signatures, mobile_responsive, dark_mode, templates, folder_locations | Create `visual--emails.json` |

### AIGen Configs with Extra Fields (not in schema)

| Section | Extra Fields in AIGen | Recommendation |
|---------|----------------------|----------------|
| content/pillars | pillar_examples | Add to schema |
| content/types | type_templates, type_cta_mapping | Add to schema |
| products/offerings | cross_sell_mapping, objection_handling | Add to schema |
| products/cta | cta_tone_by_stage, cta_urgency_rules | Add to schema |
| operations/calendar | seasonal_planning, campaign_cadence | Add to schema or batching_approach covers these |
| operations/workflow | review_checklist, brand_compliance_check | Add to schema |
| visual/colours | colour_psychology, seasonal_variations | Add to schema |
| visual/photography | editing_guidelines, composition_rules | Add to schema |
| communication/principles | response_templates, escalation_triggers | Add to schema |
| communication/clients | feedback_handling, complaint_response | Add to schema |
| audience/core | journey_stages, engagement_triggers | Add to schema |
| audience/professional | professional_language, credibility_requirements | Add to schema |
| legal/compliance | retention_periods, cookie_consent | Add to schema |
| legal/disclaimers | disclaimer_placement, disclaimer_by_content_type | Add to schema |

---

## Library Mapping Analysis

### Fields WITH Library Mappings

| Library | Section/Field | Match Type |
|---------|---------------|------------|
| **universal/tone** | voice/tone: primary_tone, secondary_tones, tone_variations | semantic |
| **universal/emoji** | content/formatting: emoji_usage, emoji_style; content/platforms: platform_emoji | category |
| **universal/cta-patterns** | products/cta: primary_cta, secondary_ctas; emails/copy: email_ctas; content/types: content_ctas | category |
| **universal/accessibility** | content/formatting: accessibility_requirements; visual/graphics: accessibility_guidelines; communication/principles: accessibility_standards | category |
| **universal/geographic** | core/contact: service_areas; audience/core: geographic_focus; legal/entity: operating_regions | exact |
| **universal/asset-locations** | visual/logos: logo_locations; visual/templates: template_locations; operations/workflow: asset_storage | exact |
| **universal/response-times** | communication/clients: response_expectations; communication/professional: response_standards; operations/workflow: turnaround_times | exact |
| **universal/sentence-starters** | voice/guidelines: opening_phrases; emails/copy: email_openers; content/formatting: content_openers | category |
| **universal/hashtags** | content/platforms: hashtag_strategy, branded_hashtags, community_hashtags | category/keyword |
| **therapeutic/vocabulary** | voice/vocabulary: preferred_terms, terms_to_avoid; communication/clients: client_language | keyword |
| **therapeutic/content-warnings** | content/formatting: content_warnings; communication/public: trigger_warnings | category |
| **therapeutic/crisis-resources** | communication/clients: crisis_resources; legal/disclaimers: emergency_contacts; content/formatting: crisis_footer | exact |
| **therapeutic/regulatory** | legal/compliance: regulatory_bodies, registration_requirements; legal/disclaimers: professional_disclaimers | exact/category |
| **therapeutic/client-outcomes** | values/client-outcomes: transformation_language; audience/core: client_journey; communication/clients: outcome_messaging | semantic |

### Fields that SHOULD Have Library Mapping but Don't

| Section/Field | Suggested Library | Reasoning |
|---------------|-------------------|-----------|
| voice/personality: archetype | universal/archetypes | Standard brand archetypes |
| content/pillars: content_seasonal | universal/awareness-days | Awareness days, holidays |
| products/cta: phrases_to_avoid | universal/cta-patterns | Anti-patterns for CTAs |
| audience/core: transformation | therapeutic/transformations | Standard transformation language |
| voice/vocabulary: brand_words | universal/brand-vocabulary | Common brand word categories |

---

## Naming Mismatches

| Location 1 | Location 2 | Issue | Recommendation |
|------------|------------|-------|----------------|
| Schema: voice/tone (file) | Library: tone | Schema file name vs library name | Consistent - OK |
| Schema: tone_descriptors | AIGen: tone_descriptors | Field name consistent | OK |
| Schema: content/formatting | Library: emoji_usage | Schema has no emoji_usage field directly | Add emoji_usage to content/formatting schema |
| Schema: operations/calendar: batching_approach | AIGen: content_batching | Different field names | Standardise to content_batching or batching_approach |
| Library: opening_phrases | Schema: voice/guidelines | No opening_phrases field in schema | Add opening_phrases to voice/guidelines |
| Library: content_openers | Schema: content/formatting | No content_openers field in schema | Add content_openers to content/formatting |
| Library: crisis_footer | Schema: content/formatting | No crisis_footer field in schema | Add crisis_footer to content/formatting |
| Library: client_language | Schema: communication/clients | No client_language field in schema | Add client_language to communication/clients |

---

## Recommendations

### Priority 1: Create Missing AIGen Configs

Create the following AIGen section configuration files:

1. `visual--graphics.json`
2. `visual--icons.json`
3. `visual--charts.json`
4. `visual--custom-categories.json`
5. `visual--templates.json`
6. `visual--emails.json`

### Priority 2: Add Missing Fields to Schemas

Update these schemas with fields present in AIGen but missing from schema:

1. **content/pillars** - Add: pillar_examples
2. **content/types** - Add: type_templates, type_cta_mapping
3. **products/offerings** - Add: cross_sell_mapping, objection_handling
4. **products/cta** - Add: cta_tone_by_stage, cta_urgency_rules
5. **operations/workflow** - Add: review_checklist, brand_compliance_check
6. **visual/colours** - Add: colour_psychology, seasonal_variations
7. **visual/photography** - Add: editing_guidelines, composition_rules
8. **communication/principles** - Add: response_templates, escalation_triggers
9. **communication/clients** - Add: feedback_handling, complaint_response
10. **audience/core** - Add: journey_stages, engagement_triggers
11. **audience/professional** - Add: professional_language, credibility_requirements
12. **legal/compliance** - Add: retention_periods, cookie_consent
13. **legal/disclaimers** - Add: disclaimer_placement, disclaimer_by_content_type

### Priority 3: Add Missing Library Mappings

Add these field-to-library mappings:

1. **voice/personality: archetype** -> Create `universal/archetypes` library
2. **content/pillars: content_seasonal** -> Create `universal/awareness-days` library
3. **audience/core: transformation** -> Map to `therapeutic/client-outcomes` or create transformation library

### Priority 4: Resolve Naming Inconsistencies

1. Standardise `batching_approach` vs `content_batching` across schema and AIGen
2. Add library-mapped fields to relevant schemas:
   - `opening_phrases` to voice/guidelines
   - `content_openers` to content/formatting
   - `crisis_footer` to content/formatting
   - `client_language` to communication/clients

---

## Summary Table: All Sections

| Section | Schema Fields | AIGen Fields | Library Fields | Status |
|---------|---------------|--------------|----------------|--------|
| core/identity | 4 | 4 | 0 | Matched |
| core/purpose | 5 | 5 | 0 | Matched |
| legal/core | 5 | 10 | 0 | Matched |
| core/contact | 3 | 8 | 1 | Matched |
| audience/core | 12 | 16 | 2 | Matched |
| audience/professional | 14 | 16 | 0 | Matched |
| audience/supporter | 12 | 12 | 0 | Matched |
| voice/personality | 2 | 2 | 0 | Matched |
| voice/tone | 3 | 3 | 3 | Matched |
| voice/vocabulary | 5 | 5 | 2 | Matched |
| voice/characteristics | 1 | 1 | 0 | Matched |
| voice/guidelines | 3 | 3 | 2 | Matched |
| values/brand | 4 | 5 | 0 | Matched |
| values/commercial | 5 | 3 | 0 | Partial |
| values/client-outcomes | 6 | 3 | 3 | Partial |
| methodology/philosophy | 2 | 2 | 0 | Matched |
| methodology/core-methodology | 7 | 4 | 0 | Partial |
| methodology/frameworks | 2 | 2 | 0 | Matched |
| content/pillars | 5 | 6 | 0 | Matched |
| content/types | 2 | 4 | 1 | Matched |
| content/platforms | 1 | 1 | 4 | Matched |
| content/formatting | 5 | 5 | 5 | Matched |
| products/cta | 6 | 5 | 3 | Partial |
| products/services | 6 | 6 | 0 | Matched |
| products/offerings | 8 | 10 | 0 | Matched |
| products/subscriptions | 5 | 5 | 0 | Matched |
| operations/calendar | 3 | 5 | 0 | Partial |
| operations/workflow | 2 | 4 | 2 | Matched |
| operations/metrics | 3 | 2 | 0 | Partial |
| emails/types | 5 | 5 | 0 | Matched |
| emails/copy | 13 | 13 | 2 | Matched |
| visual/logos | 8 | 8 | 1 | Matched |
| visual/colours | 6 | 8 | 0 | Matched |
| visual/typography | 7 | 7 | 0 | Matched |
| visual/photography | 9 | 11 | 0 | Matched |
| visual/graphics | 10 | 0 | 1 | Missing AIGen |
| visual/icons | 10 | 0 | 0 | Missing AIGen |
| visual/charts | 9 | 0 | 0 | Missing AIGen |
| visual/custom-categories | 1 | 0 | 0 | Missing AIGen |
| visual/templates | 11 | 0 | 1 | Missing AIGen |
| visual/emails | 15 | 0 | 0 | Missing AIGen |
| legal/entity | 6 | 11 | 1 | Matched |
| legal/compliance | 9 | 11 | 2 | Matched |
| legal/disclaimers | 11 | 13 | 2 | Matched |
| communication/principles | 9 | 11 | 1 | Matched |
| communication/clients | 10 | 12 | 4 | Matched |
| communication/public | 8 | 8 | 1 | Matched |
| communication/professional | 7 | 7 | 1 | Matched |
| communication/internal | 9 | 9 | 0 | Matched |

---

## Appendix: Detailed Field Inventory

### Core Identity Section

**Schema Path:** `c:\Users\Business\BrandKit\_system\core\identity\default\schema.json`

| Field | Type | In AIGen | In Library |
|-------|------|----------|------------|
| brand_name | string | Yes | No |
| brand_name_variations | object | Yes | No |
| tagline | string | Yes | No |
| positioning_statement | string | Yes | No |

### Voice Tone Section

**Schema Path:** `c:\Users\Business\BrandKit\_system\voice\tone\default\schema.json`

| Field | Type | In AIGen | In Library | Library Name |
|-------|------|----------|------------|--------------|
| tone_descriptors | object | Yes | Yes | universal/tone |
| tone_descriptors.core_qualities | array | Yes | Yes | - |
| tone_descriptors.spectrums | object | Yes | Yes | - |
| tone_descriptors.context_shifts | object | Yes | Yes | - |
| formality_level | object | Yes | No | - |
| warmth_level | object | Yes | No | - |

### Content Formatting Section

**Schema Path:** `c:\Users\Business\BrandKit\_system\content\formatting\default\schema.json`

| Field | Type | In AIGen | In Library | Library Name |
|-------|------|----------|------------|--------------|
| heading_hierarchy | object | Yes | No | - |
| paragraph_length | object | Yes | No | - |
| lists_and_structure | object | Yes | No | - |
| emphasis_and_styling | object | Yes | No | - |
| spacing_and_breaks | object | Yes | No | - |
| emoji_usage | - | No | Yes | universal/emoji |
| content_warnings | - | No | Yes | therapeutic/content-warnings |
| content_openers | - | No | Yes | universal/sentence-starters |
| crisis_footer | - | No | Yes | therapeutic/crisis-resources |

### Products CTA Section

**Schema Path:** `c:\Users\Business\BrandKit\_system\products\cta\default\schema.json`

| Field | Type | In AIGen | In Library | Library Name |
|-------|------|----------|------------|--------------|
| cta_by_content_type | object | Yes | Yes | universal/cta-patterns |
| tone | string | No | No | - |
| example_phrases | array | No | No | - |
| phrases_to_avoid | array | No | No | - |
| button_text | object | Yes | Yes | universal/cta-patterns |
| urgency_language | object | Yes | No | - |
| cta_tone_by_stage | - | Yes (AIGen only) | No | - |
| cta_urgency_rules | - | Yes (AIGen only) | No | - |

---

## File Locations Reference

### BrandKit Schema Files
```
c:\Users\Business\BrandKit\_system\{category}\{section}\default\schema.json
```

### AIGen Config Files
```
c:\Users\Business\BrandKit AIGen\sections\{category}--{section}.json
```

### Library Mapping File
```
c:\Users\Business\BrandLib\_config\field-mappings.json
```

---

*Report generated by field mapping analysis - 2026-01-13*
